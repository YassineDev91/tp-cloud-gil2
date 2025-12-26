# TP Séance 3 - Haute Disponibilité avec Docker Compose

**Durée :** 1h30  
**Objectifs :**
- Créer une architecture hautement disponible
- Comprendre le load balancing
- Tester le failover automatique
- Calculer la disponibilité d'un système

---

## Partie 1 : Préparation de l'Architecture 

### 1.1 Créer le dossier du projet

**Dans VM2, ouvrir un terminal :**

```bash
# Créer le dossier du projet
mkdir ~/ha-architecture
cd ~/ha-architecture
```

### 1.2 Créer les pages web pour chaque serveur

```bash
# Créer les dossiers pour les 3 serveurs web
mkdir web1 web2 web3

# Créer des pages HTML différentes pour chaque serveur
echo "<h1>Server 1 - Zone A</h1><p>Region: Paris</p>" > web1/index.html
echo "<h1>Server 2 - Zone B</h1><p>Region: Frankfurt</p>" > web2/index.html
echo "<h1>Server 3 - Zone C</h1><p>Region: London</p>" > web3/index.html

# Vérifier
cat web1/index.html
cat web2/index.html
cat web3/index.html
```

### 1.3 Copier les fichiers de configuration

**Copier le fichier docker-compose.yml fourni :**

```bash
# Copier depuis l'emplacement fourni par l'enseignant
cp /chemin/vers/docker-compose.yml .
cp /chemin/vers/nginx-lb.conf .

# OU créer manuellement (si fourni en format texte)
nano docker-compose.yml
# Coller le contenu fourni
```

**Contenu de docker-compose.yml :**
```yaml
version: '3'

services:
  web1:
    image: nginx:alpine
    container_name: web1
    volumes:
      - ./web1:/usr/share/nginx/html:ro
    networks:
      - webnet

  web2:
    image: nginx:alpine
    container_name: web2
    volumes:
      - ./web2:/usr/share/nginx/html:ro
    networks:
      - webnet

  web3:
    image: nginx:alpine
    container_name: web3
    volumes:
      - ./web3:/usr/share/nginx/html:ro
    networks:
      - webnet

  loadbalancer:
    image: nginx:alpine
    container_name: loadbalancer
    ports:
      - "80:80"
    volumes:
      - ./nginx-lb.conf:/etc/nginx/nginx.conf:ro
    depends_on:
      - web1
      - web2
      - web3
    networks:
      - webnet

networks:
  webnet:
    driver: bridge
```

**Contenu de nginx-lb.conf :**
```nginx
events {
    worker_connections 1024;
}

http {
    upstream backend {
        server web1:80;
        server web2:80;
        server web3:80;
    }

    server {
        listen 80;

        location / {
            proxy_pass http://backend;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
        }
    }
}
```

### 1.4 Démarrer l'architecture

```bash
# Démarrer tous les services
docker-compose up -d

# Vérifier que tout fonctionne
docker-compose ps
```

**Résultat attendu :**
```
NAME            IMAGE          STATUS
loadbalancer    nginx:alpine   Up
web1            nginx:alpine   Up
web2            nginx:alpine   Up
web3            nginx:alpine   Up
```

**Question 1 :** Combien de conteneurs sont lancés ? Quel est leur rôle ?

---

## Partie 2 : Tests de Load Balancing 

### 2.1 Test de répartition du trafic

```bash
# Faire plusieurs requêtes
curl http://localhost
curl http://localhost
curl http://localhost
curl http://localhost
curl http://localhost
curl http://localhost
```

**Observation attendue :**
```
Requête 1 : Server 1 - Zone A
Requête 2 : Server 2 - Zone B
Requête 3 : Server 3 - Zone C
Requête 4 : Server 1 - Zone A
Requête 5 : Server 2 - Zone B
Requête 6 : Server 3 - Zone C
```

**Question 2 :** Comment le trafic est-il réparti ? Quel algorithme est utilisé ?

### 2.2 Observer les logs en temps réel

**Ouvrir un deuxième terminal :**

```bash
cd ~/ha-architecture

# Voir les logs de tous les conteneurs
docker-compose logs -f
```

**Dans le premier terminal, faire des requêtes :**
```bash
curl http://localhost
```

**Observation :** Chaque requête apparaît dans les logs du serveur qui l'a traitée.

**Question 3 :** Peut-on voir dans les logs quel serveur traite chaque requête ?

### 2.3 Statistiques des conteneurs

```bash
# Observer les ressources utilisées
docker stats

# Appuyer Ctrl+C pour arrêter
```

**Question 4 :** Quelle est la consommation mémoire de chaque conteneur ?

---

## Partie 3 : Tests de Failover 

### 3.1 Panne d'un serveur (Scenario 1)

```bash
# Arrêter le serveur 1
docker-compose stop web1

# Vérifier l'état
docker-compose ps
```

**Faire immédiatement des requêtes :**
```bash
curl http://localhost
curl http://localhost
curl http://localhost
curl http://localhost
```

**Observation attendue :**
```
Requête 1 : Server 2 - Zone B
Requête 2 : Server 3 - Zone C
Requête 3 : Server 2 - Zone B
Requête 4 : Server 3 - Zone C
```

**✅ Le service continue avec les serveurs 2 et 3 !**

**Question 5 :** Le service est-il toujours accessible ? Qu'est-ce qui a changé ?

### 3.2 Panne de deux serveurs (Scenario 2)

```bash
# Arrêter également le serveur 2
docker-compose stop web2

# Vérifier
docker-compose ps
```

**Faire des requêtes :**
```bash
curl http://localhost
curl http://localhost
curl http://localhost
```

**Observation attendue :**
```
Toutes les requêtes : Server 3 - Zone C
```

**✅ Le service continue avec un seul serveur !**

**Question 6 :** Que se passerait-il si on arrête aussi web3 ?

### 3.3 Récupération automatique

```bash
# Redémarrer web1
docker-compose start web1

# Attendre 2-3 secondes
sleep 3

# Faire des requêtes
curl http://localhost
curl http://localhost
curl http://localhost
curl http://localhost
```

**Observation attendue :**
```
Requête 1 : Server 3 - Zone C
Requête 2 : Server 1 - Zone A  ← Réapparaît !
Requête 3 : Server 3 - Zone C
Requête 4 : Server 1 - Zone A
```

**✅ Le serveur 1 est automatiquement réintégré !**

**Question 7 :** Combien de temps a pris la récupération ?

### 3.4 Test de crash brutal

```bash
# Redémarrer tous les serveurs
docker-compose start web2 web3

# "Crasher" brutalement web2
docker kill web2

# Faire immédiatement des requêtes
curl http://localhost
curl http://localhost
curl http://localhost
```

**Observation :** Le trafic continue sur web1 et web3 uniquement.

**Question 8 :** Y a-t-il eu une interruption de service pour l'utilisateur ?

---

## Partie 4 : Architecture et Schémas 

### 4.1 Comprendre l'architecture

**Schéma de l'architecture créée :**

```
                    [Load Balancer]
                    (Port 80 externe)
                           |
                           | Réseau Docker: webnet
                           |
              +------------+------------+
              |            |            |
           [web1]       [web2]       [web3]
          Port 80      Port 80      Port 80
          Zone A       Zone B       Zone C
```

**Compléter le tableau :**

| Conteneur | Rôle | Zone | Port |
|-----------|------|------|------|
| loadbalancer | Répartition du trafic | N/A | 80 (externe) |
| web1 | | | |
| web2 | | | |
| web3 | | | |

### 4.2 Visualiser le réseau Docker

```bash
# Voir les réseaux Docker
docker network ls

# Inspecter le réseau webnet
docker network inspect ha-architecture_webnet
```

**Observer les IPs des conteneurs dans le réseau.**

**Question 9 :** Quelles sont les adresses IP des conteneurs web1, web2, web3 ?

---

## Partie 6 : Questions de Réflexion

### Compréhension Globale

**Question 10 :** Pourquoi utiliser 3 serveurs et pas 2 ?

**Réponse attendue :**
- Avec 2 serveurs : si 1 tombe, l'autre est à 100% de charge (risque de surcharge)
- Avec 3 serveurs : si 1 tombe, chaque survivant est à 50% → Marge de sécurité

**Question 11 :** Le Load Balancer lui-même est-il un SPOF (Single Point of Failure) ?

**Réponse attendue :**
- Oui ! Si le LB tombe, tout s'arrête
- Solution : Avoir 2 Load Balancers avec IP virtuelle (VRRP)

**Question 15 :** Citez 3 avantages de Docker Compose par rapport aux VMs pour ce TP.

**Réponses attendues :**
- Léger en ressources (RAM/CPU)
- Démarrage instantané
- Configuration simple (fichier YAML)
- Utilisé en production réelle

**Question 16 :** Dans un vrai datacenter, où placeriez-vous les 3 serveurs ?

**Réponse attendue :**
- Dans 3 zones de disponibilité différentes
- Pour se protéger contre panne d'un datacenter entier

---

## Partie 7 : Nettoyage

### 7.1 Arrêter l'architecture

```bash
# Arrêter et supprimer tous les conteneurs
docker-compose down

# Vérifier
docker-compose ps
docker ps
```

### 7.2 (Optionnel) Supprimer les images

```bash
# Si vous souhaitez libérer l'espace disque
docker rmi nginx:alpine
```

---

## Livrables

**À rendre en fin de TP :**

1. **Captures d'écran :**
   - `docker-compose ps` montrant les 4 conteneurs actifs
   - Test de failover (web1 arrêté, requêtes fonctionnent)
   - Logs montrant répartition du trafic

2. **Tableau comparatif complété :**
   - Architecture simple vs HA

3. **Réponses aux 16 questions**

---

## Aide-Mémoire

### Commandes Docker Compose essentielles

```bash
# Démarrer tous les services
docker-compose up -d

# Voir l'état des conteneurs
docker-compose ps

# Arrêter un service spécifique
docker-compose stop web1

# Redémarrer un service
docker-compose start web1

# Voir les logs
docker-compose logs -f

# Arrêter tout et nettoyer
docker-compose down
```

### Commandes Docker utiles

```bash
# Voir tous les conteneurs en cours
docker ps

# Voir les statistiques en temps réel
docker stats

# "Crasher" un conteneur
docker kill web2

# Voir les réseaux
docker network ls
```

---

## Bonus (Optionnel)

### Scaling Automatique

Si vous avez le temps, essayez :

```bash
# Augmenter le nombre de serveurs web1
docker-compose up -d --scale web1=2

# Observer
docker-compose ps
```

**Question bonus :** Que se passe-t-il avec 2 instances de web1 ?

---

## Pour Aller Plus Loin

**Lecture recommandée après le TP :**
- Documentation Docker Compose : https://docs.docker.com/compose/
- Load Balancing avec Nginx : https://nginx.org/en/docs/http/load_balancing.html
- Les "9" de disponibilité : https://en.wikipedia.org/wiki/High_availability

