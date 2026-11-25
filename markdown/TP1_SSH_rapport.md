# TP-1 : Mise en place du protocole SSH

![Logo UCAD](images/TP1/01-logo-ucad.png)

**Université Cheikh Anta Diop**  
**École Supérieure Polytechnique**  
**Département Génie Informatique**  
**Année universitaire 2026-2027**  
**Licence Génie Logiciel et Systèmes d’Information (LGLSI, 3e année)**  
**Services Réseaux Avancés**  
**Travaux pratiques**

## Informations

- **Classe :** L3 GLSI A
- **Membres du binôme :**
  - Mahuna Olympe ATCHATIN
  - Adja Sira Diop
- **Enseignant :** Dr Keba GUEYE

## Environnement

Pour ce TP, deux machines virtuelles ont servi de serveurs. Dans les deux cas, le client est sous Windows.

| Machine | Système | Adresse IP |
|---|---|---|
| Machine 1 | Debian | 192.168.1.138 |
| Machine 2 | Ubuntu | 192.168.1.93 |

## Étape 1 : Installation de SSH

On vérifie que le paquet `openssh-server` est installé.

### Machine 1 (Debian)

![Machine 1 — Vérification de openssh-server](images/TP1/02-m1-verif-openssh-server.png)

### Machine 2 (Ubuntu)

![Machine 2 — Vérification de openssh-server](images/TP1/03-m2-verif-openssh-server.png)

Ensuite, on lance le service SSH et on vérifie son état.

### Machine 1 (Debian)

![Machine 1 — Démarrage et état du service SSH](images/TP1/04-m1-demarrage-service-ssh.png)

### Machine 2 (Ubuntu)

![Machine 2 — Démarrage et état du service SSH](images/TP1/05-m2-demarrage-service-ssh.png)

On vérifie que le port 22 est ouvert.

### Machine 1 (Debian)

![Machine 1 — Vérification du port 22](images/TP1/06-m1-verif-port-22.png)

### Machine 2 (Ubuntu)

![Machine 2 — Vérification du port 22](images/TP1/07-m2-verif-port-22.png)

## Étape 2 : Connexion SSH depuis le client

On trouve l’adresse IP du serveur avec la commande `ip a`.

### Machine 2 (Ubuntu)

![Machine 2 — Recherche de l’adresse IP](images/TP1/08-m2-adresse-ip.png)

On se connecte ensuite au serveur depuis le client Windows. L’invite du serveur montre que la connexion a réussi.

### Machine 1 (Debian)

![Machine 1 — Connexion SSH depuis le client](images/TP1/09-m1-connexion-ssh.png)

### Machine 2 (Ubuntu)

![Machine 2 — Connexion SSH depuis le client](images/TP1/10-m2-connexion-ssh.png)

![Machine 2 — Session SSH ouverte](images/TP1/11-m2-session-ssh-ouverte.png)

## Étape 3 : Configuration des clés SSH

On génère les clés sur le serveur en utilisant `ssh-keygen` avec RSA.

### Machine 1 (Debian)

![Machine 1 — Génération des clés SSH](images/TP1/12-m1-generation-cles.png)

### Machine 2 (Ubuntu)

![Machine 2 — Génération des clés SSH](images/TP1/13-m2-generation-cles.png)

On vérifie la présence des fichiers générés.

### Machine 1 (Debian)

![Machine 1 — Vérification des fichiers de clés](images/TP1/14-m1-verif-fichiers-cles.png)

![Machine 1 — Contenu du dossier .ssh](images/TP1/15-m1-contenu-dossier-ssh.png)

### Machine 2 (Ubuntu)

![Machine 2 — Vérification des fichiers de clés](images/TP1/16-m2-verif-fichiers-cles.png)

![Machine 2 — Contenu du dossier .ssh](images/TP1/17-m2-contenu-dossier-ssh.png)

On copie les clés sur le client et on corrige les permissions de la clé privée. Il faut aussi copier la **clé privée** (`id_rsa`) : la clé publique seule ne suffit pas pour se connecter sans mot de passe.

### Machine 1 (Debian)

![Machine 1 — Copie des clés et permissions](images/TP1/18-m1-copie-cles.png)

![Machine 1 — Permissions de la clé privée](images/TP1/19-m1-permissions-cle-privee.png)

### Machine 2 (Ubuntu)

![Machine 2 — Copie des clés et permissions](images/TP1/20-m2-copie-cles.png)

On teste à nouveau la connexion avec la clé privée.

### Machine 1 (Debian)

![Machine 1 — Test de connexion avec la clé privée](images/TP1/21-m1-connexion-cle-privee.png)

### Machine 2 (Ubuntu)

**Problème :** le serveur demandait quand même le mot de passe. Après vérification, on se rend compte que le fichier `authorized_keys` est vide. On ajoute donc la clé publique dedans.

![Machine 2 — Ajout de la clé publique dans authorized_keys](images/TP1/22-m2-ajout-authorized-keys.png)

La connexion se fait maintenant sans mot de passe.

![Machine 2 — Connexion SSH sans mot de passe](images/TP1/23-m2-connexion-sans-mdp.png)

## Étape 4 : Tunnel SSH en ligne de commande

### Serveur web (Apache)

On crée un tunnel du port 1024 du client vers le port 80 du serveur.

#### Machine 1 (Debian)

![Machine 1 — Création du tunnel SSH vers Apache](images/TP1/24-m1-creation-tunnel-apache.png)

![Machine 1 — Tunnel SSH actif](images/TP1/25-m1-tunnel-apache-actif.png)

#### Machine 2 (Ubuntu)

On vérifie qu’Apache tourne sur le serveur.

![Machine 2 — Vérification du service Apache](images/TP1/26-m2-verif-apache.png)

On ouvre le tunnel puis, sur la machine cliente, on accède à la page par défaut d’Apache 2 via `127.0.0.1:1024`.

![Machine 2 — Ouverture du tunnel SSH](images/TP1/27-m2-ouverture-tunnel-apache.png)

![Machine 2 — Accès à Apache via le tunnel](images/TP1/28-m2-acces-apache-tunnel.png)

### Base de données (MariaDB)

On vérifie que MariaDB tourne sur le serveur.

#### Machine 2 (Ubuntu)

![Machine 2 — Vérification du service MariaDB](images/TP1/29-m2-verif-mariadb.png)

On crée une base de données ainsi qu’un utilisateur qui a les droits sur cette base. Les droits avaient été donnés à `tp_ssh` au lieu de `test_ssh` ; on les redonne sur la bonne base.

![Machine 2 — Création de la base et de l’utilisateur](images/TP1/30-m2-creation-base-utilisateur.png)

![Machine 2 — Attribution des droits sur la bonne base](images/TP1/31-m2-droits-bonne-base.png)

On ouvre ensuite un nouveau tunnel du port 3307 du client vers le port 3306 du serveur.

![Machine 2 — Création du tunnel SSH vers MariaDB](images/TP1/32-m2-creation-tunnel-mariadb.png)

On accède ensuite à la base via le client `mysql` avec le port 3307 et l’utilisateur créé. Le hostname confirme qu’on est bien connecté à la base du serveur.

![Machine 2 — Accès à MariaDB via le tunnel](images/TP1/33-m2-acces-mariadb-tunnel.png)
