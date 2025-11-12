# 1) Pré-requis & limites d’édition

* Disponible nativement via le composant **MMC** `lusrmgr.msc` sur **Windows Pro/Enterprise/Education**.
* **Windows Home** n’a pas `lusrmgr.msc`. Utilisez alors **PowerShell** / **CMD** ou *Comptes d’utilisateurs* (`netplwiz`).

# 2) Ouvrir l’outil (plusieurs méthodes)

## 2.1 Méthode directe (GUI)

* **Win + R** → tapez `lusrmgr.msc` → **Entrée**.
  Vous arrivez sur *Utilisateurs et groupes locaux* (comme sur votre capture).

## 2.2 Via Gestion de l’ordinateur

* **Win + R** → `compmgmt.msc` → *Outils système* → *Utilisateurs et groupes locaux*.

  > Remarque : absent en Home.

## 2.3 En ligne de commande

* **CMD** : `mmc.exe lusrmgr.msc`
* **PowerShell** : `Start-Process lusrmgr.msc`

## 2.4 Alternatives universelles (y compris Home)

* **Boîte de dialogue comptes** :

  * `netplwiz`  (gestion simple des comptes locaux)
  * `control userpasswords2` (mêmes options, interface classique)
* **Tout faire en PowerShell / CMD** (sections 5 et 6 ci-dessous).

# 3) Repères dans l’interface `lusrmgr.msc`

* **Utilisateurs** : comptes locaux (ex. `Administrator`, `Guest`, vos comptes).
* **Groupes** : ensembles de droits (ex. `Administrators`, `Users`, `Remote Desktop Users`, etc.).
* Clic droit sur un **utilisateur** :

  * **Définir le mot de passe**, **Renommer**, **Propriétés** (déverrouiller, désactiver, forcer changement de mot de passe).
* Clic droit sur **Groupes** → **Nouveau groupe** ; ou ouvrez un groupe existant pour **Ajouter…** des membres.

# 4) Tâches courantes dans l’interface (GUI)

## 4.1 Créer un nouvel utilisateur standard

1. *Utilisateurs* → clic droit → **Nouvel utilisateur…**
2. Renseignez *Nom d’utilisateur*, *Mot de passe*.
3. Décochez « L’utilisateur doit changer de mot de passe à la prochaine ouverture de session » si vous fixez un mot de passe définitif.
4. Validez (**Créer**). Par défaut, il est membre de **Users** (non admin).

## 4.2 Créer un administrateur local

1. Créez l’utilisateur (comme ci-dessus).
2. Ouvrez *Groupes* → **Administrators** → **Ajouter…** → entrez le nom du compte → **OK**.

## 4.3 Désactiver / réactiver un compte

* *Utilisateurs* → double-clic sur le compte → onglet **Général** → cochez/décochez **Compte désactivé**.

## 4.4 Forcer le changement de mot de passe

* *Utilisateurs* → double-clic → **L’utilisateur doit changer de mot de passe à la prochaine ouverture de session**.

## 4.5 Définir / réinitialiser le mot de passe

* *Utilisateurs* → clic droit sur le compte → **Définir le mot de passe…**.

## 4.6 Gérer le compte **Guest** et **Administrator**

* Par sécurité : **laissez Guest désactivé**.
* L’admin intégré **Administrator** peut rester désactivé si vous avez un autre admin.

## 4.7 Donner l’accès RDP (Bureau à distance)

* *Groupes* → **Remote Desktop Users** → **Ajouter…** votre utilisateur.

# 5) Équivalents **CMD (net.exe)** — fonctionne aussi en Home

> Ouvrir **Invite de commandes (Admin)**

### 5.1 Lister

```bat
net user
net localgroup
net localgroup administrators
```

### 5.2 Créer un compte

```bat
net user alice M0tDeP@ss! /add
```

### 5.3 Ajouter au groupe Administrators

```bat
net localgroup administrators alice /add
```

### 5.4 Désactiver / réactiver

```bat
net user alice /active:no
net user alice /active:yes
```

### 5.5 Forcer changement de mot de passe

```bat
net user alice /logonpasswordchg:yes
```

### 5.6 Définir que le mot de passe n’expire pas

```bat
net user alice /passwordchg:yes /passwordreq:yes
wmic useraccount where name='alice' set PasswordExpires=False
```

### 5.7 Supprimer un compte

```bat
net user alice /del
```

# 6) Équivalents **PowerShell** (module LocalAccounts) — recommandé

> Ouvrir **PowerShell (Admin)**

### 6.1 Lister

```powershell
Get-LocalUser
Get-LocalGroup
Get-LocalGroupMember -Group "Administrators"
```

### 6.2 Créer un utilisateur avec mot de passe sécurisé

```powershell
$pwd = ConvertTo-SecureString 'M0tDeP@ss!' -AsPlainText -Force
New-LocalUser -Name 'alice' -FullName 'Alice Martin' -Password $pwd -PasswordNeverExpires:$false
```

### 6.3 Ajouter aux groupes

```powershell
Add-LocalGroupMember -Group 'Administrators' -Member 'alice'
Add-LocalGroupMember -Group 'Remote Desktop Users' -Member 'alice'
```

### 6.4 Désactiver / réactiver

```powershell
Disable-LocalUser -Name 'alice'
Enable-LocalUser  -Name 'alice'
```

### 6.5 Forcer le changement de mot de passe à la prochaine connexion

```powershell
Set-LocalUser -Name 'alice' -Password $pwd
# (Option explicite non fournie par LocalAccounts ; utilisez une GPO ou net.exe)
```

### 6.6 Supprimer un utilisateur

```powershell
Remove-LocalUser -Name 'alice'
```

# 7) Scénarios prêts à l’emploi

## 7.1 Créer un **compte standard** pour un utilisateur local

```powershell
$pwd = ConvertTo-SecureString 'N0uV3@u!' -AsPlainText -Force
New-LocalUser -Name 'etudiant' -FullName 'Compte Étudiant' -Password $pwd
# Rien d’autre : il reste dans le groupe Users (non admin).
```

## 7.2 Créer un **admin local** d’urgence

```powershell
$pwd = ConvertTo-SecureString 'Adm1n-Urgence!' -AsPlainText -Force
New-LocalUser -Name 'adminsecours' -Password $pwd
Add-LocalGroupMember -Group 'Administrators' -Member 'adminsecours'
```

## 7.3 Compte **temporaire** qui expire à une date donnée

```powershell
$pwd = ConvertTo-SecureString 'Temp123!' -AsPlainText -Force
New-LocalUser -Name 'invité' -Password $pwd
Set-LocalUser -Name 'invité' -AccountExpires (Get-Date).AddDays(7)
```

## 7.4 Autoriser l’utilisateur à se connecter en **RDP**

```powershell
Add-LocalGroupMember -Group 'Remote Desktop Users' -Member 'alice'
```

# 8) Gérer à distance (machine distante)

## 8.1 MMC distant (Pro/Entreprise)

* **Win + R** → `compmgmt.msc`
* Clic droit **Gestion de l’ordinateur (local)** → **Se connecter à un autre ordinateur…** → nom/IP.
* Naviguez vers *Utilisateurs et groupes locaux* (si autorisé par le pare-feu et les droits).

## 8.2 PowerShell distant (WinRM)

* Sur la cible (Admin) :

  ```powershell
  Enable-PSRemoting -Force
  ```
* Depuis votre poste (Admin) :

  ```powershell
  Enter-PSSession -ComputerName PC-CIBLE -Credential (Get-Credential)
  # Puis utilisez les commandes LocalAccounts à distance.
  ```

# 9) Bonnes pratiques de sécurité

* **Désactivez** le compte **Guest**.
* Conservez **au moins un** compte admin local secondaire, mot de passe robuste.
* Nommez les comptes clairement, évitez d’utiliser l’admin intégré pour l’usage quotidien.
* Pour RDP : donnez l’accès via **Remote Desktop Users**, pas via Administrators si ce n’est pas nécessaire.
* Appliquez des mots de passe complexes et changez-les si compromis.
* Journalisez les échecs de connexion (Observateur d’événements → `eventvwr.msc` → **Security**).

# 10) Dépannage (erreurs fréquentes)

* **“Windows ne trouve pas lusrmgr.msc”** : vous êtes sur **Windows Home**. Utilisez **PowerShell/CMD** ou `netplwiz`.
* **“Vous ne disposez pas des autorisations”** : ouvrez l’outil **en tant qu’administrateur**.
* **MMC vide à distance** : pare-feu/ports DCOM/Remote Registry désactivés ou absence de droits admin sur la cible.
* **Échec ajout groupe** : vérifiez l’orthographe du compte et du groupe, et que le compte est **local** (pas compte Microsoft/cloud).

# 11) Fiche mémo (cheat-sheet)

* Ouvrir : `lusrmgr.msc` | `compmgmt.msc` | `netplwiz`
* Lister : `Get-LocalUser` | `net user`
* Créer :

  ```powershell
  $pwd = ConvertTo-SecureString 'Xxx!' -AsPlainText -Force
  New-LocalUser -Name 'nom' -Password $pwd
  ```
* Admin : `Add-LocalGroupMember -Group 'Administrators' -Member 'nom'`
* Désactiver : `Disable-LocalUser -Name 'nom'`
* RDP : `Add-LocalGroupMember -Group 'Remote Desktop Users' -Member 'nom'`

