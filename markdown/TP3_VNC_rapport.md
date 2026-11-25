# TP-3 : Configuration et utilisation de VNC

![Logo UCAD](images/TP3/01-logo-ucad.png)

**Université Cheikh Anta Diop**  
**École Supérieure Polytechnique**  
**Département Génie Informatique**  
**Année universitaire 2026-2027**  
**Licence Génie Logiciel et Systèmes d’Information (LGLSI, 3e année)**  
**Services Réseaux Avancés**  
**Travaux pratiques**

## Informations

- **Classe :** L3 GLSI A
- **Étudiant :** Mahuna Olympe ATCHATIN
- **Enseignant :** Dr Keba GUEYE

## Partie 1 : Installation et configuration du serveur VNC

### 1. Mise à jour de la machine

```bash
sudo apt update && sudo apt upgrade -y
```

![Mise à jour de la machine](images/TP3/02-mise-a-jour.png)

### 2. Installation du serveur VNC

On installe TigerVNC :

```bash
sudo apt install tigervnc-standalone-server -y
```

![Installation de TigerVNC](images/TP3/03-installation-tigervnc.png)

### 3. Configuration de l’environnement de bureau

```bash
sudo apt install xfce4 xfce4-goodies -y
```

![Installation de XFCE](images/TP3/04-installation-xfce.png)

Pendant l’installation, on nous demande quel gestionnaire d’affichage garder. On garde **gdm3**, celui d’Ubuntu par défaut, pour ne rien changer à la VM.

![Choix du gestionnaire d’affichage gdm3](images/TP3/05-choix-gdm3.png)

### 4. Initialisation du serveur VNC

On démarre le serveur VNC pour créer la configuration initiale.

![Initialisation du serveur VNC](images/TP3/06-initialisation-vnc.png)

Le port 5901 étant déjà occupé, on utilise le 5902.

### 5. Arrêter le serveur pour la configuration avancée

![Arrêt du serveur VNC](images/TP3/07-arret-serveur-vnc.png)

### 6. Personnaliser le fichier de configuration

On édite le fichier de configuration `~/.vnc/xstartup`.

![Édition du fichier xstartup](images/TP3/08-edition-xstartup.png)

Puis on s’assure que le script est exécutable.

![Rendre le script xstartup exécutable](images/TP3/09-xstartup-executable.png)

### 7. Relancer le serveur VNC

**Problème :** une fois lancé, le serveur s’arrêtait automatiquement.

Sur Ubuntu 24.04, TigerVNC considère que la session graphique dure aussi longtemps que le script `xstartup`. Dès que ce script se termine, il arrête le serveur VNC. Or, à cause du `&`, la commande `startxfce4` est lancée en arrière-plan : le script n’attend pas XFCE et se termine immédiatement. TigerVNC conclut que la session est finie et arrête le serveur.

![Arrêt automatique du serveur VNC](images/TP3/10-arret-automatique-serveur.png)

**Solution :** `exec startxfce4` remplace `startxfce4 &` : le script est remplacé par XFCE au lieu de se terminer. La session reste ouverte tant que XFCE tourne.

- Les deux lignes `unset` retirent les variables de la session GNOME déjà ouverte, pour éviter que XFCE tente de s’y rattacher (ce qui donne un écran noir ou gris).

![Fichier xstartup corrigé](images/TP3/11-xstartup-corrige.png)

![Relance du serveur VNC](images/TP3/12-serveur-vnc-lance.png)

Le serveur est maintenant lancé.

## Partie 2 : Connexion depuis une machine cliente

### 1. Installation du client VNC sur la machine cliente

On installe VNC Viewer sur la machine cliente Windows.

![Installation du client VNC sur Windows](images/TP3/13-installation-client-vnc.png)

### 2. Connexion au serveur

On ouvre TigerVNC, puis on renseigne l’IP du serveur ainsi que le numéro de port utilisé.

![Saisie de l’IP et du port du serveur](images/TP3/14-saisie-ip-port.png)

**Problème :** le client affiche une erreur de connexion. Après analyse, il s’est avéré que TigerVNC applique par défaut l’option `localhost=yes`, ce qui fait que le serveur n’écoute que sur l’adresse locale.

![Erreur de connexion du client VNC](images/TP3/15-erreur-connexion.png)

**Solution :** on tue le processus en cours, puis on le relance avec l’option `localhost` à `no` :

```bash
vncserver -kill :1
vncserver :1 -localhost no -geometry 1600x900
```

![Relance du serveur avec -localhost no](images/TP3/16-relance-localhost-no.png)

On retente ensuite la connexion depuis le client. Elle passe cette fois-ci, et on renseigne le mot de passe défini au premier lancement de VNC.

![Saisie du mot de passe VNC](images/TP3/17-saisie-mot-de-passe.png)

### 3. Test de la connexion

Une fois connecté, l’environnement de bureau du serveur apparaît. On peut alors tester les interactions.

![Bureau du serveur affiché dans le client VNC](images/TP3/18-bureau-distant.png)

## Partie 3 : Sécurisation de la connexion VNC avec un tunnel SSH

### 1. Configuration du tunnel SSH

Avant de créer le tunnel, on relance le serveur VNC avec l’option par défaut `localhost` à `yes`. Puis on crée le tunnel SSH.

![Création du tunnel SSH](images/TP3/19-tunnel-ssh.png)

### 2. Connexion au serveur VNC via le tunnel

![Connexion au serveur VNC via le tunnel](images/TP3/20-connexion-via-tunnel.png)

On renseigne ensuite le mot de passe du serveur VNC et l’écran de la VM s’affiche.

![Bureau de la VM via le tunnel SSH](images/TP3/21-bureau-via-tunnel.png)

### 3. Vérifier le chiffrement

En consultant les journaux SSH, on constate que la connexion est bien chiffrée.

![Journaux SSH montrant la connexion chiffrée](images/TP3/22-journaux-ssh.png)
