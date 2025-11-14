# Énoncé — Ajout et gestion des comptes locaux sous **Ubuntu 24.04 LTS**

## Sommaire

* [Objectif](#objectif)
* [Contexte](#contexte)
* [Compétences visées](#compétences-visées)
* [Prérequis & consignes](#prérequis--consignes)
* [Rappels sécurité & limites](#rappels-sécurité--limites)
* [Correction — Méthode 1 (GUI GNOME)](#correction--méthode-1--gui-gnome)
* [Correction — Méthode 2 (CLI)](#correction--méthode-2--cli)
* [Scénarios rapides](#scénarios-rapides)
* [Dépannage](#dépannage)
* [Fiche mémo](#fiche-mémo)

---

<details>
  <summary><strong>Objectif</strong></summary>

Mettre en place, pas à pas, la gestion des comptes <strong>locaux</strong> sous Ubuntu 24.04 : création d’utilisateurs standard, élévation en administrateur (groupe <code>sudo</code>), accès à distance (OpenSSH / xRDP en option), réinitialisation/désactivation, suppression, appartenance aux groupes et paramètres de mots de passe (expiration/rotation). Deux approches sont proposées : <strong>tout au clic (GNOME Settings)</strong> et <strong>en ligne de commande (Terminal)</strong>.

</details>

<details>
  <summary><strong>Contexte</strong></summary>

Dans un poste de travail ou un labo, on doit souvent créer rapidement un compte “cours-prof” (standard), un compte “support-admin” (administrateur local via <code>sudo</code>) ou un compte temporaire “invite”, tout en sécurisant l’accès SSH/RDP et en journalisant les connexions.

</details>

<details>
  <summary><strong>Compétences visées</strong></summary>

* Gérer les utilisateurs/groupes via **Paramètres GNOME** et via **Terminal**.
* Créer, réinitialiser, verrouiller/déverrouiller, supprimer des comptes.
* Déléguer l’administration via le groupe **sudo** et gérer l’appartenance à d’autres groupes.
* Configurer l’accès distant de base (SSH, optionnellement xRDP) et les règles de mots de passe (expiration).

</details>

<details>
  <summary><strong>Prérequis & consignes</strong></summary>

<strong>Prérequis</strong>

* Ubuntu **24.04 LTS** (Desktop ou Server).
* Compte disposant de droits **sudo**.
* Accès physique ou distant (SSH) au système.

<strong>Consignes de réalisation</strong>

1. Choisir une méthode (GUI ou CLI). Bonus si vous faites les deux.
2. Créer l’utilisateur standard <code>cours-prof</code> avec mot de passe.
3. Créer l’utilisateur <code>support-admin</code> et l’ajouter au groupe <code>sudo</code>.
4. Activer et tester l’accès à distance souhaité (SSH recommandé ; xRDP optionnel en Desktop).
5. Appliquer une politique de sécurité minimale (verrouillage si nécessaire, rotation/expiration du mot de passe).
6. Journaliser et consigner vos commandes/sorties dans un mini-rapport (captures/terminal).
7. Nettoyer les comptes de test ou les désactiver selon le contexte.

<strong>Checklist rapide</strong>

* [ ] <code>cours-prof</code> créé (standard, mot de passe défini)
* [ ] <code>support-admin</code> créé (+ groupe <code>sudo</code>)
* [ ] SSH activé et testé (ou RDP si requis sur Desktop)
* [ ] Politique de mot de passe revue (<code>chage</code> / verrouillage si besoin)
* [ ] Journalisation et mini-rapport produits

</details>

<details>
  <summary><strong>Rappels sécurité & limites</strong></summary>

* Ne jamais publier de mots de passe en clair ; privilégier les **clés SSH** plutôt que l’authentification par mot de passe.
* Désactiver **root** en SSH (<code>PermitRootLogin no</code>) et si possible désactiver <code>PasswordAuthentication</code> (authentification par clé).
* Limiter les membres de <code>sudo</code> au strict nécessaire et suivre les logs d’authentification.

</details>

---

## Correction — Méthode 1 · GUI GNOME

<details>
  <summary><strong>A. Ouvrir la gestion des utilisateurs</strong></summary>

Paramètres → **Utilisateurs** → cliquer sur **Déverrouiller** (saisir votre mot de passe).

</details>

<details>
  <summary><strong>B. Créer un utilisateur standard</strong></summary>

**Ajouter utilisateur** → renseigner Nom/Identifiant → choisir **Compte standard** → définir un mot de passe → **Ajouter**.

</details>

<details>
  <summary><strong>C. Donner les droits administrateur (sudo)</strong></summary>

Dans **Utilisateurs** → sélectionner le compte → basculer le rôle sur **Administrateur** (équivaut à appartenance au groupe <code>sudo</code>).

</details>

<details>
  <summary><strong>D. Gérer le mot de passe / verrouillage</strong></summary>

Dans **Utilisateurs** → sélectionner le compte → changer le mot de passe, exiger un mot de passe fort, ou **désactiver** le compte si nécessaire.

</details>

<details>
  <summary><strong>E. Accès à distance (aperçu Desktop)</strong></summary>

Pour SSH, installer **OpenSSH Server** (via Terminal). Pour bureau à distance, installer **xrdp** (optionnel), ouvrir le port dans **ufw**, tester la connexion.

</details>

---

## Correction — Méthode 2 · CLI

> Toutes les commandes suivantes s’exécutent dans un **Terminal** avec un compte ayant les droits **sudo**.

<details>
  <summary><strong>Créer un utilisateur standard</strong></summary>

```bash
# Méthode interactive (recommandée sur Ubuntu)
sudo adduser cours-prof

# Méthode non interactive (exemple)
sudo useradd -m -s /bin/bash cours-prof
echo 'cours-prof:MotDePasseFort' | sudo chpasswd
```

</details>

<details>
  <summary><strong>Donner les droits administrateur (sudo)</strong></summary>

```bash
sudo usermod -aG sudo support-admin
# Vérifier
groups support-admin
```

</details>

<details>
  <summary><strong>Réinitialiser le mot de passe</strong></summary>

```bash
sudo passwd cours-prof
```

</details>

<details>
  <summary><strong>Verrouiller / déverrouiller un compte</strong></summary>

```bash
# Verrouiller (désactive l’authentification par mot de passe)
sudo passwd -l cours-prof
# Déverrouiller
sudo passwd -u cours-prof
```

</details>

<details>
  <summary><strong>Forcer le changement de mot de passe / expiration</strong></summary>

```bash
# Forcer un changement à la prochaine connexion
sudo chage -d 0 cours-prof

# Définir une expiration à 7 jours
sudo chage -E $(date -d "+7 days" +%F) invite
```

</details>

<details>
  <summary><strong>Supprimer un utilisateur</strong></summary>

```bash
# Supprimer sans effacer /home
sudo deluser cours-prof

# Supprimer et effacer le répertoire /home et le mail spool
sudo userdel -r cours-prof
```

</details>

<details>
  <summary><strong>Gérer l’appartenance à d’autres groupes</strong></summary>

```bash
# Ajouter à un groupe existant (ex. docker)
sudo usermod -aG docker cours-prof

# Lister les groupes de l’utilisateur
groups cours-prof
```

</details>

<details>
  <summary><strong>Activer l’accès SSH (recommandé)</strong></summary>

```bash
# Installer et démarrer OpenSSH Server
sudo apt update && sudo apt install -y openssh-server
sudo systemctl enable --now ssh

# (Option) Durcir /etc/ssh/sshd_config puis recharger
sudo sed -i 's/^#PermitRootLogin.*/PermitRootLogin no/' /etc/ssh/sshd_config
# sudo sed -i 's/^#PasswordAuthentication yes/PasswordAuthentication no/' /etc/ssh/sshd_config
sudo systemctl reload ssh

# UFW : autoriser SSH et (option) xRDP
sudo ufw allow OpenSSH
# sudo ufw allow 3389/tcp   # si xrdp
```

</details>

<details>
  <summary><strong>Activer un bureau à distance (optionnel Desktop)</strong></summary>

```bash
# xRDP sur Ubuntu Desktop
sudo apt update && sudo apt install -y xrdp
sudo systemctl enable --now xrdp
sudo adduser $USER ssl-cert   # recommandé pour RDP sur Ubuntu
sudo ufw allow 3389/tcp
```

</details>

---

## Scénarios rapides

<details>
  <summary><strong>Créer “cours-prof” (standard) et forcer changement de mot de passe</strong></summary>

```bash
sudo adduser cours-prof
sudo chage -d 0 cours-prof
```

</details>

<details>
  <summary><strong>Créer “support-admin” (admin local)</strong></summary>

```bash
sudo adduser support-admin
sudo usermod -aG sudo support-admin
```

</details>

<details>
  <summary><strong>Créer “invite” qui expire dans 7 jours</strong></summary>

```bash
sudo adduser invite
sudo chage -E $(date -d "+7 days" +%F) invite
```

</details>

<details>
  <summary><strong>Activer SSH sécurisé (sans root, mot de passe autorisé par défaut)</strong></summary>

```bash
sudo apt install -y openssh-server
sudo systemctl enable --now ssh
sudo sed -i 's/^#PermitRootLogin.*/PermitRootLogin no/' /etc/ssh/sshd_config
sudo systemctl reload ssh
sudo ufw allow OpenSSH
```

</details>

---

## Dépannage

* « user is not in the sudoers file » → se connecter avec un compte ayant sudo, puis : `sudo usermod -aG sudo <user>`.
* « Authentication failure » → réinitialiser le mot de passe (`sudo passwd <user>`), vérifier les verrous (`passwd -S <user>`).
* Impossible de supprimer l’utilisateur → fermer ses sessions (`pkill -KILL -u <user>`), puis `sudo userdel -r <user>`.
* Problème SSH → vérifier le service (`systemctl status ssh`), les ports (`ss -tulpn | grep ssh`), le pare-feu (`sudo ufw status`), et les logs (`sudo journalctl -u ssh -e`).
* Échec xRDP (écran bleu/connexion rejetée) → ajouter l’utilisateur à `ssl-cert`, redémarrer `xrdp`, vérifier `ufw`, s’assurer que la session GNOME est disponible.

---

## Fiche mémo

* Créer (interactif) : `sudo adduser <user>`
* Créer (non interactif) : `sudo useradd -m -s /bin/bash <user> && echo '<user>:<PASS>' | sudo chpasswd`
* Admin (sudo) : `sudo usermod -aG sudo <user>`
* Verrouiller / déverrouiller : `sudo passwd -l <user>` / `sudo passwd -u <user>`
* Forcer rotation : `sudo chage -d 0 <user>`
* Expiration datée : `sudo chage -E YYYY-MM-DD <user>`
* Supprimer : `sudo deluser <user>` | `sudo userdel -r <user>`
* Groupes : `groups <user>` | `sudo usermod -aG <group> <user>`
* SSH : `sudo apt install -y openssh-server && sudo systemctl enable --now ssh && sudo ufw allow OpenSSH`

---

<details>
  <summary><strong>Annexe — Toutes les commandes</strong></summary>

### 1) Création / suppression d’utilisateurs

```bash
# Création (interactif, recommandé)
sudo adduser alice

# Création (non interactif)
sudo useradd -m -s /bin/bash alice
echo 'alice:M0tDeP@ss!' | sudo chpasswd

# Suppression
sudo deluser alice
sudo userdel -r alice    # supprime aussi /home/alice
```

### 2) Groupes et privilèges

```bash
# Donner les droits sudo
sudo usermod -aG sudo alice
groups alice

# Ajouter à d'autres groupes (ex. docker)
sudo usermod -aG docker alice
```

### 3) Mots de passe, verrouillage, expiration

```bash
# Réinitialiser
sudo passwd alice

# Verrouiller / Déverrouiller
sudo passwd -l alice
sudo passwd -u alice
passwd -S alice   # état du compte

# Rotation à la prochaine connexion
sudo chage -d 0 alice

# Expiration à une date donnée
sudo chage -E 2025-12-31 alice

# Politique (exemples globaux via PAM/login.defs, non détaillés ici)
```

### 4) Accès SSH (recommandé)

```bash
# Installer et activer
sudo apt update && sudo apt install -y openssh-server
sudo systemctl enable --now ssh

# Durcir la config
sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.bak
sudo sed -i 's/^#PermitRootLogin.*/PermitRootLogin no/' /etc/ssh/sshd_config
# sudo sed -i 's/^#PasswordAuthentication yes/PasswordAuthentication no/' /etc/ssh/sshd_config
sudo systemctl reload ssh

# Pare-feu
sudo ufw allow OpenSSH
sudo ufw status
```

### 5) Bureau à distance (optionnel Desktop)

```bash
# Installer xRDP
sudo apt update && sudo apt install -y xrdp
sudo systemctl enable --now xrdp
sudo adduser $USER ssl-cert
sudo ufw allow 3389/tcp
```

### 6) Audit et logs

```bash
# Connexions/échecs SSH
sudo journalctl -u ssh -e

# Dernières connexions locales/distant
last
lastb            # connexions échouées (si journalisées)

# Journal système
journalctl -xe   # erreurs récentes
```

### 7) Modèles “réutilisables”

```bash
# Créer un standard et forcer rotation
u=cours-prof; sudo adduser "$u"; sudo chage -d 0 "$u"

# Créer un admin local
u=support-admin; sudo adduser "$u"; sudo usermod -aG sudo "$u"

# Créer un invité qui expire dans 7 jours
u=invite; sudo adduser "$u"; sudo chage -E "$(date -d '+7 days' +%F)" "$u"
```

### 8) Réseau minimal pour accès distant

```bash
# Vérifier écoute SSH
ss -tulpn | grep ssh

# Ouvrir SSH dans ufw
sudo ufw allow OpenSSH
```

#### Annexe condensée (copier-coller rapide)

```bash
# Standard + rotation
sudo adduser cours-prof && sudo chage -d 0 cours-prof

# Admin local
sudo adduser support-admin && sudo usermod -aG sudo support-admin

# SSH durci
sudo apt install -y openssh-server && sudo systemctl enable --now ssh
sudo sed -i 's/^#PermitRootLogin.*/PermitRootLogin no/' /etc/ssh/sshd_config
sudo systemctl reload ssh
sudo ufw allow OpenSSH
```

</details>
