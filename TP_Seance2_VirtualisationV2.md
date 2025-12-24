# TP Séance 2 - Virtualisation et Disaster Recovery


**Niveau :** DI-GIL2
**Durée :** 2 heures  
**Objectifs :**
- Comprendre l'isolation entre machines virtuelles
- Observer la mutualisation des ressources
- Comparer VM et Container
- Utiliser les snapshots pour la récupération après incident

---

## Matériel Nécessaire

- VirtualBox installé
- VM1 : Linux Mint (déjà créée lors de la Séance 1)
- 1 fichier .ova fourni :
  - `VM2-Container.ova` (Linux Mint + Docker)
- Script `simulation.sh` fourni

---

## Partie 0 : Préparation - Installation des services

**Important :** Cette étape doit être effectuée AVANT de créer les snapshots.

### 0.1 Installation Nginx sur VM1 (Container)

**Démarrer VM1 et ouvrir un terminal :**

```bash
# Vérifier l'installation
docker --version

# Lancer un conteneur Nginx
docker run -d --name webserver -p 80:80 nginx

# Vérifier que le conteneur fonctionne
docker ps

# Tester
curl http://localhost
```

**Résultat attendu :** Vous devriez voir la page par défaut de Nginx.

### 0.2 Installation Nginx natif sur VM2

**Démarrer VM2 et ouvrir un terminal :**

```bash
# Mettre à jour les paquets
sudo apt update

# Installer Nginx
sudo apt install nginx -y

# Vérifier l'installation
systemctl status nginx

# Tester
curl http://localhost
```

**Résultat attendu :** Vous devriez voir la page par défaut de Nginx.

---

## Partie 1 : Import et Configuration

### 1.1 Import de VM2

1. Ouvrir VirtualBox
2. Fichier → Importer un appareil virtuel
3. Sélectionner `VM2-Container.ova` → Importer

### 1.2 Configuration réseau

**Pour VM1 et VM2 :**
1. Sélectionner la VM → Configuration → Réseau
2. Carte 2 : Activer
3. Mode d'accès réseau : Réseau privé hôte
4. Cliquer OK

### 1.3 Démarrage et vérification

**Démarrer VM1 et VM2**

**Dans VM1 (Container) :**
```bash
docker ps
curl http://localhost
```

**Dans VM2 (Natif) :**
```bash
systemctl status nginx
curl http://localhost
```

**Vérifier les IPs :**
```bash
# Dans chaque VM
ip addr show
# Noter les IPs (ex: 192.168.56.101 et 192.168.56.102)
```

---

## Partie 2 : Tests d'Isolation 

### 2.1 Isolation des processus

**Dans VM1 :**
```bash
# Lancer un processus gourmand
yes > /dev/null &

# Observer
top
```

**Dans VM2 :**
```bash
# Observer les processus
top
```

**Question 1 :** Voyez-vous les processus de VM1 dans VM2 ? Pourquoi ?

**Arrêter les processus dans VM1 :**
```bash
pkill yes
```

### 2.2 Isolation réseau

**Depuis VM1 :**
```bash
curl http://192.168.56.102
```

**Depuis VM2 :**
```bash
curl http://192.168.56.101
```

**Question 2 :** Les deux VMs peuvent-elles communiquer ?

### 2.3 Test de crash

**Éteindre brutalement VM1 depuis VirtualBox**

**Dans VM2 :**
```bash
# Vérifier que VM2 fonctionne toujours
uptime
curl http://localhost

# Essayer de contacter VM1
ping 192.168.56.101 -c 4
```

**Question 3 :** Que se passe-t-il quand VM1 crashe ? VM2 est-elle affectée ?

**Redémarrer VM1**

---

## Partie 3 : Observation Mutualisation

### 3.1 Observation des ressources dans VirtualBox

**Avec les 2 VMs en marche, observer dans VirtualBox :**
- CPU utilisé par chaque VM
- RAM utilisée par chaque VM
- Total des ressources du PC hôte

### 3.2 Test charge CPU

**Dans VM1 :**
```bash
yes > /dev/null &
```

**Observer dans VirtualBox :**
- Utilisation CPU de VM1
- Utilisation CPU de VM2
- Total

**Question 4 :** Les ressources sont-elles partagées ? Comment ?

**Dans VM2 (simultanément) :**
```bash
yes > /dev/null &
```

**Observer l'utilisation totale du CPU**

**Question 5 :** Que se passe-t-il quand les 2 VMs demandent beaucoup de CPU ?

**Nettoyer :**
```bash
# Dans VM1 et VM2
pkill yes
```

---

## Partie 4 : Comparaison VM vs Container

### 4.1 Observer les différences

**Dans VM1 (Container) :**
```bash
# Voir le container
docker ps

# Redémarrer le container (chronométrer)
time docker restart webserver

# Vérifier
curl http://localhost
```

**Dans VM2 (Natif) :**
```bash
# Redémarrer Nginx (chronométrer)
time sudo systemctl restart nginx

# Vérifier
curl http://localhost
```

**Question 6 :** Quelle approche est plus rapide ? Pourquoi ?

### 4.2 Tableau comparatif

Compléter le tableau suivant :

| Critère | VM1 (Container) | VM2 (Natif) |
|---------|-----------------|-------------|
| Temps redémarrage | | |
| Facilité gestion | | |
| Isolation | | |

---

## Partie 5 : Création des Snapshots

**Pour VM1 :**
1. Éteindre VM1
2. Dans VirtualBox → Onglet "Instantanés"
3. Créer un instantané
4. Nom : `Etat_Sain_Avant_Incident`
5. Description : `Système propre, Nginx opérationnel`
6. Redémarrer VM1

**Répéter pour VM2**

**Question 7 :** À quoi sert un snapshot ?

---

## Partie 6 : Scénario Incident

### 6.1 Contexte

> 🚨 **ALERTE SÉCURITÉ**  
> Votre infrastructure a été compromise par un ransomware.  
> VM2 est infectée. Vous devez rétablir le service.

### 6.2 Exécution de l'attaque (sur VM2)

**Copier le script fourni dans VM2 :**
```bash
chmod +x simulation.sh
./simulation.sh
```

**Observer les messages du script**

### 6.3 Constat des dégâts

```bash
# Page web compromise
curl http://localhost

# Service Nginx
systemctl status nginx

# Fichiers créés
ls ~/*.encrypted
```

**Question 8 :** Quels sont les dégâts visibles ?

### 6.4 Tentative de réparation manuelle

**Essayez de réparer sans utiliser les snapshots :**

```bash
# Redémarrer Nginx
sudo systemctl start nginx

# Essayer de restaurer la page web
# ???

# Supprimer les fichiers malveillants
rm ~/*.encrypted
```

**Question 9 :** Arrivez-vous à tout réparer ? Combien de temps ça prend ?

### 6.5 Solution : Restauration du snapshot

**Dans VirtualBox :**
1. Éteindre VM2
2. Onglet Instantanés
3. Clic droit sur `Etat_Sain_Avant_Incident`
4. Restaurer
5. Confirmer
6. Démarrer VM2

**Vérifier la restauration :**
```bash
# Page web restaurée
curl http://localhost

# Nginx fonctionne
systemctl status nginx

# Fichiers malveillants disparus
ls ~/*.encrypted
```

**Question 10 :** Combien de temps a pris la restauration ? Tout est-il revenu à la normale ?

### 6.6 Vérification de VM1

**Question 11 :** VM1 a-t-elle été affectée par l'attaque sur VM2 ?

```bash
# Dans VM1
curl http://localhost
docker ps
```


---

## Livrables

**À rendre en fin de TP :**
1. Document avec captures d'écran des tests d'isolation
2. Tableau comparatif VM vs Container complété
3. Réponses aux 11 questions

---

## Critères d'évaluation

| Critère | Points |
|---------|--------|
| Tests d'isolation réalisés | /4 |
| Observation mutualisation | /3 |
| Comparaison VM vs Container | /3 |
| Création snapshots | /2 |
| Scénario incident géré | /5 |
| Qualité des réponses | /3 |
| **TOTAL** | **/20** |
