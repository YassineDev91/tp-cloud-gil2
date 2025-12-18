# TRAVAUX PRATIQUES - SÉANCE 1
## Découverte des Modèles IaaS, PaaS et SaaS

**Formation :** DI-GIL2  
**Durée :** 2 heures  

## OBJECTIFS

À la fin de ce TP, vous serez capables de :

1. **Créer et configurer** une machine virtuelle (modèle IaaS)
2. **Déployer** une application web sur une plateforme PaaS réelle
3. **Utiliser** un logiciel SaaS professionnel (MES)
4. **Comparer** concrètement les trois modèles de service


<!-- ## ORGANISATION DU TP

**Travail :** En binôme  
**Rendu :** Document PDF avec captures d'écran et réponses aux questions  
**Date de rendu :**  -->

**Structure du TP :**
- Partie 1 : IaaS avec VirtualBox 
- Partie 2 : PaaS avec Render.com 
- Partie 3 : SaaS avec Odoo 
---

## PRÉREQUIS TECHNIQUES

### Logiciels à installer

**1. VirtualBox (gratuit)**
- Téléchargement : https://www.virtualbox.org/wiki/Downloads
- Choisir la version pour votre système (Windows, Mac, Linux)
- Installation : suivre l'assistant d'installation standard

**2. Image ISO Ubuntu**
- Téléchargement : https://ubuntu.com/download/desktop
- Choisir : Ubuntu 22.04 LTS Desktop
- Taille : environ 4 GB

**Vérification :** VirtualBox est correctement installé si vous pouvez lancer l'application.

### Comptes à créer

**1. Compte GitHub**
- Site : https://github.com/
- Nécessaire pour : déployer sur Render.com

**2. Compte Render.com**
- Site : https://render.com/
- Inscription gratuite (pas de carte bancaire)
- Se connecter avec GitHub (recommandé)

**3. Compte Odoo**
- Site : https://www.odoo.com/trial
- Inscription gratuite pour démo

---

# PARTIE 1 - IaaS : Infrastructure as a Service avec VirtualBox

**Objectif :** Créer une machine virtuelle, la configurer et y installer un serveur web manuellement.

**Durée estimée :** 40 minutes

**Concept :** Dans le modèle IaaS, vous gérez tout sauf le matériel physique. Vous devez configurer l'OS, installer les logiciels, gérer la sécurité.


## Étape 1.1 : Créer une machine virtuelle 

### 1. Lancer VirtualBox

Ouvrez l'application VirtualBox sur votre ordinateur.

### 2. Créer une nouvelle VM

**Cliquez sur "Nouvelle" (ou "New")**

**Configuration de la VM :**

- **Nom :** `TP-IaaS-Cloud`
- **Type :** Linux
- **Version :** Ubuntu (64-bit)
- **Cliquez sur "Suivant"**

**Mémoire vive (RAM) :**

- **Allocation :** 2048 MB (2 GB)
- Si votre ordinateur a plus de 8 GB de RAM, vous pouvez allouer 4096 MB
- **Cliquez sur "Suivant"**

**Disque dur :**

- Sélectionnez : **"Créer un disque dur virtuel maintenant"**
- **Cliquez sur "Créer"**

**Type de fichier de disque :**

- Sélectionnez : **VDI (VirtualBox Disk Image)**
- **Cliquez sur "Suivant"**

**Stockage :**

- Sélectionnez : **"Dynamiquement alloué"**
- **Cliquez sur "Suivant"**

**Taille du disque :**

- **Taille :** 20 GB (suffisant pour ce TP)
- **Cliquez sur "Créer"**

### 3. Configurer la VM avant démarrage

**Sélectionnez votre VM** dans la liste et **cliquez sur "Configuration"**

**Onglet "Stockage" :**
- Cliquez sur "Vide" sous "Contrôleur: IDE"
- Cliquez sur l'icône CD (à droite)
- Sélectionnez "Choisir un fichier de disque..."
- Sélectionnez votre fichier ISO Ubuntu téléchargé
- **Cliquez sur "OK"**

**Onglet "Réseau" :**
- Mode d'accès réseau : **NAT** (par défaut)
- **Cliquez sur "OK"**


## Étape 1.2 : Installer Ubuntu

### 1. Démarrer la VM

**Sélectionnez votre VM** et **cliquez sur "Démarrer"**

La VM démarre sur l'ISO Ubuntu.

### 2. Installation d'Ubuntu

**Écran de démarrage :**
- Sélectionnez votre langue : **Français** (ou English)
- Cliquez sur **"Installer Ubuntu"**

**Disposition du clavier :**
- Sélectionnez votre clavier (French pour clavier français)
- **Continuer**

**Mises à jour et autres logiciels :**
- Sélectionnez : **"Installation normale"**
- Décochez : "Télécharger les mises à jour pendant l'installation" (pour gagner du temps)
- **Continuer**

**Type d'installation :**
- Sélectionnez : **"Effacer le disque et installer Ubuntu"**
- (Ne vous inquiétez pas, c'est le disque virtuel, pas votre ordinateur)
- **Installer maintenant** > **Continuer**

**Fuseau horaire :**
- Sélectionnez votre localisation
- **Continuer**

**Qui êtes-vous ?**
- **Votre nom :** [Votre prénom]
- **Nom de l'ordinateur :** tp-iaas-cloud
- **Nom d'utilisateur :** etudiant
- **Mot de passe :** cloud2025 (simple pour le TP)
- **Continuer**

**Attendez l'installation** (5-10 minutes)

### 3. Redémarrage

Une fois l'installation terminée :
- Cliquez sur **"Redémarrer maintenant"**
- Appuyez sur **Entrée** quand demandé

**Connexion :**
- Sélectionnez l'utilisateur "etudiant"
- Entrez le mot de passe : cloud2025

**Félicitations ! Votre VM Ubuntu est opérationnelle.**


## Étape 1.3 : Installer un serveur web Apache

Maintenant que l'OS est installé, vous devez TOUT configurer manuellement. C'est ça, l'IaaS.

### 1. Ouvrir le Terminal

- Cliquez sur "Activités" (coin supérieur gauche)
- Tapez "Terminal"
- Cliquez sur l'application Terminal

### 2. Mettre à jour le système

Dans le terminal, tapez les commandes suivantes :

```bash
sudo apt update
```

**Entrez votre mot de passe** quand demandé : `cloud2025`

### 3. Installer Apache

```bash
sudo apt install apache2 -y
```

Attendez l'installation (2-3 minutes).

### 4. Vérifier qu'Apache fonctionne

```bash
sudo systemctl status apache2
```

Vous devez voir **"active (running)"** en vert.

Appuyez sur **Q** pour quitter.

### 5. Créer une page web personnalisée

```bash
echo "<h1>Bienvenue sur mon serveur IaaS</h1>" | sudo tee /var/www/html/index.html
echo "<p>Créé par : [VOTRE NOM]</p>" | sudo tee -a /var/www/html/index.html
echo "<p>Modèle : IaaS avec VirtualBox</p>" | sudo tee -a /var/www/html/index.html
echo "<p>Système : Ubuntu 22.04</p>" | sudo tee -a /var/www/html/index.html
```

**Remplacez [VOTRE NOM] par votre nom réel.**

### 6. Tester le serveur web

**Ouvrir Firefox** dans la VM :
- Activités > Firefox
- Dans la barre d'adresse, tapez : `http://localhost`
- Appuyez sur **Entrée**

**Vous devez voir votre page web personnalisée !**


## Livrable Partie 1

**Captures d'écran à inclure dans votre rapport :**

1. VirtualBox montrant votre VM en cours d'exécution
2. Terminal Ubuntu avec la commande `sudo systemctl status apache2` montrant "active (running)"
3. Navigateur Firefox affichant votre page web avec votre nom

---

# PARTIE 2 - PaaS : Platform as a Service avec Render.com

**Objectif :** Déployer une application web sans gérer l'infrastructure sous-jacente.


**Concept :** Dans le modèle PaaS, vous gérez uniquement le code. Le fournisseur gère l'OS, le serveur web, le runtime, le scaling.

---

## Étape 2.1 : Préparer le code de l'application

Nous allons déployer une application web simple en Python (Flask).

### 1. Créer un compte GitHub (si pas déjà fait)

- Allez sur : https://github.com/
- Cliquez sur "Sign up"
- Suivez les instructions

### Créer votre propre dépôt :

1. **Créer un nouveau dépôt sur GitHub**
   - Cliquez sur "+" en haut à droite > "New repository"
   - Nom : `tp-paas-cloud`
   - Visibilité : Public
   - Cochez : "Add a README file"
   - Cliquez sur "Create repository"

2. **Ajouter les fichiers de l'application**

Cliquez sur "Add file" > "Create new file"

**Fichier 1 : `app.py`**

```python
from flask import Flask
import socket
import os

app = Flask(__name__)

@app.route('/')
def hello():
    hostname = socket.gethostname()
    return f'''
    <!DOCTYPE html>
    <html>
    <head>
        <title>Application PaaS</title>
        <style>
            body {{
                font-family: Arial, sans-serif;
                max-width: 800px;
                margin: 50px auto;
                padding: 20px;
                background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
                color: white;
            }}
            .container {{
                background: rgba(255, 255, 255, 0.1);
                padding: 30px;
                border-radius: 10px;
                box-shadow: 0 8px 32px rgba(0, 0, 0, 0.1);
            }}
            h1 {{ color: #fff; }}
            .info {{ 
                background: rgba(255, 255, 255, 0.2);
                padding: 15px;
                border-radius: 5px;
                margin: 15px 0;
            }}
        </style>
    </head>
    <body>
        <div class="container">
            <h1>🚀 Bienvenue sur mon application PaaS !</h1>
            <div class="info">
                <p><strong>Créé par :</strong> [VOTRE NOM]</p>
                <p><strong>Modèle :</strong> PaaS avec Render.com</p>
                <p><strong>Hostname :</strong> {hostname}</p>
                <p><strong>Langage :</strong> Python + Flask</p>
            </div>
            <div class="info">
                <h2>Ce que je N'AI PAS eu à faire :</h2>
                <ul>
                    <li>❌ Créer une machine virtuelle</li>
                    <li>❌ Installer un système d'exploitation</li>
                    <li>❌ Configurer un serveur web</li>
                    <li>❌ Installer Python</li>
                    <li>❌ Gérer le scaling</li>
                    <li>❌ Gérer les certificats SSL</li>
                </ul>
                <h2>Ce que j'AI fait :</h2>
                <ul>
                    <li>✅ Écrire le code de l'application</li>
                    <li>✅ Cliquer sur "Deploy"</li>
                    <li>✅ C'est tout ! 🎉</li>
                </ul>
            </div>
        </div>
    </body>
    </html>
    '''

if __name__ == '__main__':
    port = int(os.environ.get('PORT', 10000))
    app.run(host='0.0.0.0', port=port)
```

**Remplacez [VOTRE NOM] par votre nom réel avant de sauvegarder.**

- Name : `app.py`
- Cliquez sur "Commit new file"

**Fichier 2 : `requirements.txt`**

- Cliquez sur "Add file" > "Create new file"

```
Flask==3.0.0
```

- Name : `requirements.txt`
- Cliquez sur "Commit new file"

**Fichier 3 : `render.yaml` (optionnel mais recommandé)**

- Cliquez sur "Add file" > "Create new file"

```yaml
services:
  - type: web
    name: tp-paas-cloud
    env: python
    buildCommand: pip install -r requirements.txt
    startCommand: python app.py
```

- Name : `render.yaml`
- Cliquez sur "Commit new file"

**Votre dépôt est prêt !**

---

## Étape 2.2 : Créer un compte Render.com 

### 1. S'inscrire sur Render

- Allez sur : https://render.com/
- Cliquez sur "Get Started"
- **Recommandé :** Cliquez sur "Sign up with GitHub"
- Autorisez Render à accéder à vos dépôts

**Aucune carte bancaire n'est requise.**

### 2. Vérifier votre email

Consultez votre boîte email et cliquez sur le lien de vérification.


## Étape 2.3 : Déployer l'application

### 1. Créer un nouveau Web Service

Dans le dashboard Render :

- Cliquez sur "New +" (en haut à droite)
- Sélectionnez "Web Service"

### 2. Connecter votre dépôt GitHub

- Vous voyez la liste de vos dépôts GitHub
- Cliquez sur "Connect" à côté de `tp-paas-cloud`

Si vous ne voyez pas votre dépôt :
- Cliquez sur "Configure account"
- Autorisez l'accès au dépôt

### 3. Configurer le service

**Name :** `tp-paas-cloud-[votre-nom]` (doit être unique)

**Region :** Choisissez "Frankfurt (EU Central)" (le plus proche)

**Branch :** `main` (ou `master`)

**Runtime :** Python 3

**Build Command :**
```
pip install -r requirements.txt
```

**Start Command :**
```
python app.py
```

**Plan :** **Free** (0 $ / mois)

- ⚠️ Limitations du plan gratuit :
  - Le service se met en veille après 15 min d'inactivité
  - Redémarre au premier accès (délai ~30 secondes)

### 4. Déployer

Cliquez sur **"Create Web Service"**

**Render va automatiquement :**

1. Cloner votre code depuis GitHub
2. Créer un environnement Python
3. Installer les dépendances (Flask)
4. Démarrer votre application
5. Générer une URL HTTPS

**Attendez le déploiement** (2-5 minutes)

### 5. Suivre les logs en temps réel

Dans la section "Logs", vous voyez en temps réel :

```
==> Cloning from https://github.com/[vous]/tp-paas-cloud...
==> Downloading cache...
==> Installing dependencies
Collecting Flask==3.0.0
  Downloading Flask-3.0.0-py3-none-any.whl
Successfully installed Flask-3.0.0
==> Build successful 🎉
==> Starting service with 'python app.py'
 * Running on http://0.0.0.0:10000
==> Your service is live 🎉
```

### 6. Accéder à votre application

En haut de la page, vous voyez l'URL de votre application :

`https://tp-paas-cloud-[votre-nom].onrender.com`

**Cliquez sur l'URL** ou copiez-la dans votre navigateur.

**🎉 Votre application est en ligne, accessible depuis n'importe où dans le monde, avec HTTPS automatique !**


## Étape 2.4 : Observer les fonctionnalités PaaS (10 min)

### 1. Logs en temps réel

- Allez dans l'onglet "Logs"
- Actualisez votre page web plusieurs fois
- Vous voyez les requêtes HTTP dans les logs en temps réel

### 2. Déploiement automatique

**Testons le déploiement continu :**

1. Retournez sur GitHub dans votre dépôt `tp-paas-cloud`
2. Cliquez sur le fichier `app.py`
3. Cliquez sur l'icône crayon (Edit)
4. Modifiez le titre, par exemple :
   ```python
   <h1>🚀 Mon Application PaaS Améliorée !</h1>
   ```
5. Cliquez sur "Commit changes"

**Retournez sur Render :**

- Dans les logs, vous voyez automatiquement :
  ```
  ==> New commit detected
  ==> Starting new deployment...
  ==> Build successful
  ==> Deploy successful
  ```

**Actualisez votre page web : le changement est appliqué !**

### 3. Métriques (si disponibles)

Explorez l'onglet "Metrics" :
- CPU usage
- Memory usage
- Request count


## Étape 2.6 : Nettoyage (optionnel)

**Si vous voulez supprimer votre service après le TP :**

- Dashboard Render > Votre service
- Settings > Scroll en bas
- "Delete Web Service"

**Note :** Le plan gratuit n'a pas de limite, vous pouvez garder votre application en ligne.


## Livrable Partie 2

**Captures d'écran à inclure dans votre rapport :**

1. Dashboard Render montrant votre service "Live"
2. Navigateur affichant votre application avec l'URL `onrender.com`
3. Section "Logs" montrant le déploiement réussi
4. Votre dépôt GitHub avec le code de l'application

---

# PARTIE 3 - SaaS : Software as a Service avec Odoo

**Objectif :** Utiliser un logiciel métier professionnel sans aucune installation ni configuration technique.


**Concept :** Dans le modèle SaaS, vous utilisez directement le logiciel. Le fournisseur gère absolument tout : infrastructure, plateforme, application, mises à jour, sécurité.

---

## Étape 3.1 : Créer un compte Odoo (5 min)

### Qu'est-ce qu'Odoo ?

Odoo est un ERP (Enterprise Resource Planning) open-source. Il inclut de nombreux modules dont un **MES (Manufacturing Execution System)** pour la gestion de production industrielle.

### 1. S'inscrire pour un essai gratuit

- Allez sur : https://www.odoo.com/trial
- **Email :** Utilisez votre email étudiant
- **Mot de passe :** Créez un mot de passe sécurisé
- **Type d'entreprise :** Sélectionnez "Manufacturing" (Fabrication)
- **Nom de l'entreprise :** `TP Cloud - [Votre Nom]`
- **Pays :** Maroc
- **Nombre d'employés :** 1-10

**Cliquez sur "Start now"**

### 2. Vérifier votre email

- Consultez votre boîte email
- Cliquez sur le lien de vérification

**Vous êtes redirigé vers votre instance Odoo personnelle !**

**URL de votre instance :** `https://[votre-entreprise].odoo.com`

---

## Étape 3.2 : Activer le module Manufacturing (MES)

### 1. Accéder aux Applications

- Dans le menu principal, cliquez sur "Apps" (Applications)
- Vous voyez tous les modules disponibles

### 2. Installer le module Manufacturing

- Dans la barre de recherche, tapez "Manufacturing"
- Trouvez l'application "Manufacturing"
- Cliquez sur "Install" (Installer)

**Attendez l'installation** (30 secondes)

### 3. Configuration initiale

Odoo vous pose quelques questions :

**Gérez-vous des variantes de produits ?**
- Sélectionnez "Non" (pour simplifier)

**Utilisez-vous plusieurs étapes de fabrication ?**
- Sélectionnez "Oui" (pour voir les fonctionnalités MES)

**Gérez-vous la sous-traitance ?**
- Sélectionnez "Non"

**Cliquez sur "Apply"**

**Le module Manufacturing est maintenant activé !**

Vous avez accès à un vrai MES professionnel, sans avoir installé quoi que ce soit.

---

## Étape 3.3 : Scénario - Gérer une production

Nous allons simuler la production d'un produit simple : une **chaise en bois**.

### Scénario :

Vous êtes une entreprise qui fabrique des chaises. Vous allez :
1. Créer la fiche du produit "Chaise en bois"
2. Définir sa nomenclature (liste des composants)
3. Créer un ordre de fabrication
4. Suivre la production étape par étape (comme dans un vrai atelier)

### 1. Créer un produit

**Menu principal > Manufacturing > Products > Create**

**Informations du produit :**

- **Product Name :** Chaise en Bois
- **Can be Sold :** Coché (on peut la vendre)
- **Can be Manufactured :** Coché (on peut la fabriquer)

**Onglet "General Information" :**
- **Product Type :** Stockable Product
- **Invoicing Policy :** Ordered quantities

**Cliquez sur "Save"**

### 2. Créer la nomenclature (Bill of Materials)

**Restez sur la fiche du produit, cliquez sur l'onglet "Bill of Materials"**

**Cliquez sur "Create"**

Vous allez définir de quoi est composée la chaise :

**Component 1 - Planches de bois :**

- Cliquez sur "Add a line"
- **Product :** Créez un nouveau produit : "Planche de Bois"
  - Product Type : Stockable
  - Can be Purchased : Coché
  - Cliquez sur "Save & Close"
- **Quantity :** 4 (4 planches)

**Component 2 - Vis :**

- Cliquez sur "Add a line"
- **Product :** Créez un nouveau produit : "Vis"
  - Product Type : Stockable
  - Can be Purchased : Coché
  - Cliquez sur "Save & Close"
- **Quantity :** 20 (20 vis)

**Component 3 - Colle à bois :**

- Cliquez sur "Add a line"
- **Product :** Créez un nouveau produit : "Colle à Bois"
  - Product Type : Consumable (consommable)
  - Can be Purchased : Coché
  - Cliquez sur "Save & Close"
- **Quantity :** 1

**Cliquez sur "Save"**

**Votre nomenclature est créée !**

### 3. Définir les étapes de fabrication

**Toujours dans la Bill of Materials, onglet "Operations"**

Nous allons définir les étapes de production :

**Opération 1 - Découpe :**

- Cliquez sur "Add a line"
- **Operation :** Créez "Découpe des Planches"
- **Work Center :** Créez "Atelier Découpe"
- **Duration :** 30 minutes
- Cliquez sur "Save & Close"

**Opération 2 - Assemblage :**

- Cliquez sur "Add a line"
- **Operation :** Créez "Assemblage"
- **Work Center :** Créez "Atelier Assemblage"
- **Duration :** 45 minutes
- Cliquez sur "Save & Close"

**Opération 3 - Finition :**

- Cliquez sur "Add a line"
- **Operation :** Créez "Ponçage et Finition"
- **Work Center :** Créez "Atelier Finition"
- **Duration :** 20 minutes
- Cliquez sur "Save & Close"

**Cliquez sur "Save"**

### 4. Créer un ordre de fabrication

**Menu principal > Manufacturing > Operations > Manufacturing Orders**

**Cliquez sur "Create"**

**Informations de l'ordre :**

- **Product :** Chaise en Bois
- **Quantity :** 5 (vous voulez produire 5 chaises)
- **Bill of Materials :** Sélectionnez la nomenclature créée

**Cliquez sur "Confirm"**

**L'ordre de fabrication est créé !**

Odoo calcule automatiquement :
- Les matières premières nécessaires : 20 planches, 100 vis, 5 colles
- Les étapes de production
- Le temps total estimé

### 5. Suivre la production (Fonctionnalité MES)

**Cliquez sur "Work Orders" (en haut)**

Vous voyez les 3 étapes de production pour vos 5 chaises :

1. **Découpe des Planches** - 30 min x 5 = 2h30
2. **Assemblage** - 45 min x 5 = 3h45
3. **Ponçage et Finition** - 20 min x 5 = 1h40

**Simulons la production :**

**Étape 1 - Découpe :**

- Cliquez sur la ligne "Découpe des Planches"
- Cliquez sur "Start" (Démarrer)
  - L'opérateur démarre le travail, le chronomètre se lance
- Attendez 5 secondes (on simule)
- Cliquez sur "Done" (Terminer)
  - L'étape est terminée

**Étape 2 - Assemblage :**

- Retour aux Work Orders
- Cliquez sur "Assemblage"
- Cliquez sur "Start"
- Attendez 5 secondes
- Cliquez sur "Done"

**Étape 3 - Finition :**

- Retour aux Work Orders
- Cliquez sur "Ponçage et Finition"
- Cliquez sur "Start"
- Attendez 5 secondes
- Cliquez sur "Done"

**Retournez à votre Manufacturing Order**

**Cliquez sur "Produce All"**

**Toutes vos 5 chaises sont maintenant produites !**

### 6. Consulter les statistiques

**Menu Manufacturing > Reporting > Work Centers Performance**

Vous voyez :
- Le temps passé sur chaque atelier
- Le taux d'occupation
- Les ordres de fabrication terminés

## Livrable Partie 3

**Captures d'écran à inclure dans votre rapport :**

1. Dashboard Odoo montrant les applications installées
2. Fiche produit "Chaise en Bois" avec la nomenclature
3. Manufacturing Order créé avec les 5 chaises
4. Work Orders montrant les 3 étapes de production
5. Vue "Done" avec les 5 chaises produites


## RESSOURCES COMPLÉMENTAIRES

### Documentation

**VirtualBox :**
- Manuel utilisateur : https://www.virtualbox.org/manual/

**Render.com :**
- Documentation : https://render.com/docs
- Tutoriels Python : https://render.com/docs/deploy-flask

**Odoo :**
- Documentation Manufacturing :https://www.odoo.com/documentation/19.0/applications/inventory_and_mrp/manufacturing.html

## SUPPORT

**En cas de problème technique :**
- Email : [y.aithsain@ensem.ac.ma](mailto:y.aithsain@ensem.ac.ma)

**Bon courage!**
