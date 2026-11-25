# TP-2 : Configuration et utilisation de Remote Desktop Protocol (RDP)

![Logo UCAD](images/TP2/01-logo-ucad.png)

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

## Environnement

| Machine | Système | Nom d’hôte | Adresse IP |
|---|---|---|---|
| Machine hôte | Windows 11 Pro | ATCHATIN-Olympe | 192.168.56.105 |
| Serveur Linux (VM) | Ubuntu | amo-srv1 | 192.168.56.106 |

## Partie 1 : Activer le Bureau à distance sur le serveur Windows

### 1. Vérification de la version de Windows

![Vérification de la version de Windows](images/TP2/02-version-windows.png)

La version de Windows de ma machine est bien Pro. Elle peut donc servir de serveur RDP.

### 2. Activer le Bureau à distance

![Activation du Bureau à distance](images/TP2/03-activation-bureau-distance.png)

On note le nom de la machine ainsi que son IP pour les utiliser plus tard :

- **Hostname :** ATCHATIN-Olympe
- **IP :** 192.168.56.105

### 3. Configurer l’accès des utilisateurs

![Configuration des utilisateurs autorisés](images/TP2/04-utilisateurs-autorises.png)

On ajoute le compte `amo` à la liste des utilisateurs.

### 4. Vérifier les paramètres du pare-feu

![Paramètres du pare-feu](images/TP2/05-parametres-pare-feu.png)

On s’assure que les règles **Bureau à distance (TCP-In)** et **Bureau à distance (UDP-In)** sont activées.

![Règles du pare-feu pour le Bureau à distance](images/TP2/06-regles-pare-feu-rdp.png)

## Partie 2 : Connexion au serveur RDP depuis un client Windows

Pour cette partie, la machine Linux sert de serveur et la machine hôte Windows sert de client.

- **Hostname du serveur Linux :** amo-srv1
- **IP :** 192.168.56.106

### Préparation du serveur

On installe xrdp :

```bash
sudo apt install xrdp -y
```

![Installation de xrdp](images/TP2/07-installation-xrdp.png)

On lance ensuite le service et on vérifie son état.

![Démarrage et état du service xrdp](images/TP2/08-statut-service-xrdp.png)

On crée maintenant un tunnel SSH sur la machine cliente pour faire passer le trafic RDP à travers une connexion SSH chiffrée et authentifiée :

```bash
ssh -L 13389:127.0.0.1:3389 mahuna@192.168.56.106
```

![Création du tunnel SSH pour RDP](images/TP2/09-tunnel-ssh-rdp.png)

### Ouvrir l’outil Connexion Bureau à distance

![Ouverture de l’outil Connexion Bureau à distance](images/TP2/10-ouverture-outil-rdp.png)

### Saisir les informations de connexion

![Saisie des informations de connexion](images/TP2/11-informations-connexion.png)

### Gérer les options de connexion

On configure les options de connexion.

![Options de connexion (1)](images/TP2/12-options-connexion-1.png)

![Options de connexion (2)](images/TP2/13-options-connexion-2.png)

![Options de connexion (3)](images/TP2/14-options-connexion-3.png)

### Prendre le contrôle à distance

On clique sur **Connexion** et on arrive sur l’interface de xrdp, où il faut renseigner les identifiants d’un compte autorisé sur la machine serveur.

![Écran de connexion xrdp](images/TP2/15-ecran-connexion-xrdp.png)

**Problème :** lors de la première tentative, la connexion RDP s’ouvrait sur un écran noir puis se fermait aussitôt. L’utilisateur `mahuna` avait déjà une session graphique ouverte localement sur la VM, et une même session ne peut pas être affichée à la fois en local et via xrdp. Le problème a été résolu en se déconnectant de la session graphique locale avant de lancer la connexion RDP.

Une fois la connexion établie, on peut interagir avec le serveur comme si on se trouvait devant.

![Session RDP ouverte sur le serveur Linux](images/TP2/16-session-rdp-ouverte.png)

![Interaction avec le serveur via RDP](images/TP2/17-interaction-serveur.png)

## Partie 3 : Connexion au serveur RDP depuis un client Linux

### Installer un client RDP (Remmina)

On installe Remmina avec la commande :

```bash
sudo apt install remmina remmina-plugin-rdp
```

![Installation de Remmina](images/TP2/18-installation-remmina.png)

### Lancer Remmina et configurer la connexion

On ouvre Remmina et, après avoir cliqué sur **Nouvelle connexion**, on remplit les informations : le protocole, l’IP du serveur, le nom d’utilisateur et le mot de passe.

![Configuration de la connexion dans Remmina](images/TP2/19-configuration-remmina.png)

On accepte ensuite le certificat.

![Acceptation du certificat](images/TP2/20-acceptation-certificat.png)

### Tester la connexion

![Connexion RDP depuis Remmina](images/TP2/21-test-connexion-remmina.png)

## Partie 4 : Activation du serveur RDP de VirtualBox pour accéder à une VM

Dans VirtualBox, dans la configuration de la VM, on remplit les champs de la rubrique **Affichage**. Le port 3389 est déjà occupé par un processus : on en choisit donc un autre qui soit libre, ici le **5000**.

![Configuration de l’affichage de la VM dans VirtualBox](images/TP2/22-virtualbox-affichage.png)

![Activation du serveur de bureau à distance sur le port 5000](images/TP2/23-virtualbox-port-5000.png)

Ensuite, on démarre la VM. Il ne reste plus qu’à s’y connecter avec RDP en utilisant l’IP de la machine hôte (192.168.56.105).

![Connexion RDP vers la machine hôte](images/TP2/24-connexion-rdp-hote.png)

Comme le port par défaut est occupé, il faut préciser le nouveau port.

![Saisie de l’adresse avec le port 5000](images/TP2/25-saisie-port-5000.png)

![Connexion à la VM via le port 5000](images/TP2/26-connexion-vm-port-5000.png)

L’interface de la VM s’affiche ensuite : on peut s’y connecter et la manipuler.

![Interface de la VM via RDP](images/TP2/27-interface-vm.png)

### Manipulation

![Manipulation de la VM à distance](images/TP2/28-manipulation-vm.png)
