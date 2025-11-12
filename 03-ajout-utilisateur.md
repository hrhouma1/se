# Énoncé — Ajout et gestion des comptes locaux sous Windows (Pro/Entreprise & Home)

## Objectif

<details>
  <summary>
Mettre en place, pas à pas, la gestion des comptes **locaux** sous Windows 10/11 : création d’utilisateurs standard, élévation en administrateur, attribution de l’accès **Bureau à distance (RDP)**, réinitialisation/désactivation de comptes, et sécurisation des comptes intégrés. Deux approches sont proposées : **tout au clic (GUI)** et **en ligne de commande (CMD/PowerShell)**.
  </summary> 

  </details>
  
## Contexte

Dans un poste de travail ou un labo pédagogique, il est souvent nécessaire d’ajouter rapidement un compte “cours-prof” (standard), un compte “support-admin” (administrateur local), ou un compte “invité” temporaire, tout en respectant les bonnes pratiques de sécurité.

## Compétences visées

* Naviguer dans les consoles Windows (*Gestion de l’ordinateur*, `lusrmgr.msc`, `netplwiz`).
* Créer/administrer des comptes et des groupes locaux (Users, Administrators, Remote Desktop Users).
* Exécuter l’équivalent en **CMD** (`net.exe`) et en **PowerShell** (module **LocalAccounts**).
* Appliquer des règles de base de **sécurisation** (désactivation de *Guest*, mot de passe robuste, journalisation).

## Prérequis

* Windows **Pro/Entreprise/Éducation** pour `lusrmgr.msc`.
* Windows **Home** : utiliser **Paramètres**, **Panneau de configuration**, **CMD/PowerShell** (*pas* de `lusrmgr.msc` ni d’hôte RDP).
* Session **administrateur** locale pour toutes les opérations système.

## Consignes de réalisation

1. **Choisis une méthode** (Méthode 1 : GUI, Méthode 2 : CMD/PowerShell). Les deux peuvent être réalisées pour bonus.
2. **Crée** un compte standard nommé `cours-prof` (ou équivalent), avec mot de passe défini.
3. **Crée** un compte admin local `support-admin` et ajoute-le au groupe **Administrators**.
4. **(Option Pro/Entreprise)** Ajoute `cours-prof` au groupe **Remote Desktop Users** (et **active RDP** côté Paramètres).
5. **Sécurise** : désactive le compte **Guest** et laisse l’**Administrator** intégré désactivé si un autre admin existe.
6. **Teste** : connexion, changement/réinitialisation de mot de passe, désactivation/réactivation d’un compte.
7. **(PowerShell/CMD)** Répète les opérations en ligne de commande et conserve l’historique.

## Livrables attendus

* **Rapport bref** (1–2 pages) listant : étapes réalisées, choix (GUI vs CLI), difficultés et solutions.
* **Captures d’écran** clefs (ou sorties de commandes) : création de compte, appartenance aux groupes, RDP, désactivation de *Guest*.
* **Cheat-sheet** personnelle : 5–10 commandes PowerShell/CMD que tu retiens pour refaire l’exercice rapidement.

## Critères d’évaluation

* Exactitude technique (comptes créés, groupes appropriés, RDP correctement attribué en Pro/Entreprise).
* Maîtrise des **deux** niveaux : GUI **ou** CLI (bonus si les deux).
* Sécurité minimale appliquée (Guest désactivé, mots de passe robustes, admin intégré non utilisé au quotidien).
* Clarté du rapport et pertinence des preuves (captures/sorties).

## Rappels sécurité & limites

* Ne partage **jamais** de mot de passe en clair dans le rapport public.
* Windows **Home** n’expose pas le rôle serveur RDP : utiliser **Aide rapide** ou un outil tiers pour l’assistance distante.
* Journalisez et surveillez les échecs de connexion (**Observateur d’événements → Security**).

**Durée indicative** : 30–45 min (GUI) ; 20–30 min (CLI) après prise en main.




# Correction

<details>
  <summary>☰ Méthode 1 - Windows Pro / Entreprise — Ajout d'utilisateurs tout au clic </summary>


# Windows Pro / Entreprise — tout au clic

## A. Ouvrir l’outil

1. Ouvre le **Menu Démarrer** → tape **“Gestion de l’ordinateur”** → clique sur l’application.
2. Dans la fenêtre, à gauche : **Outils système** → **Utilisateurs et groupes locaux**.

   * Si tu vois *Utilisateurs* et *Groupes*, tu es au bon endroit.

## B. Créer un utilisateur local (non admin)

1. **Utilisateurs** (colonne de gauche) → clic droit dans la liste → **Nouvel utilisateur…**
2. Renseigne **Nom d’utilisateur**, **Mot de passe** (deux fois).
3. Décoche **L’utilisateur doit changer de mot de passe…** si tu veux un mot de passe fixe.
4. Clique **Créer**, puis **Fermer**.

## C. Passer un compte en administrateur

1. Va dans **Groupes** → double-clique **Administrators**.
2. **Ajouter…** → tape le **nom** du compte créé → **OK** → **Appliquer / OK**.

## D. Autoriser l’accès Bureau à distance (RDP)

1. **Groupes** → double-clique **Remote Desktop Users**.
2. **Ajouter…** → choisis le compte → **OK** → **Appliquer / OK**.

   > (RDP doit être activé dans *Paramètres* → *Système* → *Bureau à distance*.)

## E. Réinitialiser le mot de passe

1. **Utilisateurs** → clic droit sur le compte → **Définir le mot de passe…** → **Continuer** → nouveau mot de passe → **OK**.

## F. Désactiver / réactiver un compte

1. **Utilisateurs** → double-clique le compte → onglet **Général**.
2. Coche **Compte désactivé** (ou décoche pour réactiver) → **OK**.

## G. Sécuriser les comptes intégrés

* **Guest** : double-clique → **Compte désactivé** (recommandé).
* **Administrator** intégré : laisse-le **désactivé** si tu as déjà un autre admin.

---

# Windows Home — tout au clic (sans “Utilisateurs et groupes locaux”)

## A. Créer un compte local

1. **Paramètres** → **Comptes** → **Famille et autres utilisateurs**.
2. Section **Autres utilisateurs** → **Ajouter un compte**.
3. Clique **Je ne dispose pas des informations de connexion de cette personne**.
4. Clique **Ajouter un utilisateur sans compte Microsoft**.
5. Saisis **Nom d’utilisateur** + **Mot de passe** → **Suivant**.

## B. Passer le compte en administrateur

1. Reviens à **Paramètres** → **Comptes** → **Famille et autres utilisateurs**.
2. Sous **Autres utilisateurs**, clique le compte → **Changer le type de compte**.
3. Choisis **Administrateur** → **OK**.

## C. Changer le mot de passe d’un compte local

1. **Panneau de configuration** (app classique) → **Comptes d’utilisateurs** → **Gérer un autre compte**.
2. Sélectionne le compte → **Modifier le mot de passe**.

> Note : Windows Home **ne propose pas** le rôle “Remote Desktop Users” côté serveur (la fonctionnalité hôte RDP n’est pas incluse). Pour un accès à distance, utilise plutôt **Assistant d’accès rapide** (Paramètres → Système → **Aide rapide**) ou un outil tiers.

---

# Petits scénarios guidés

## Créer un compte “cours-prof” non admin (Pro/Entreprise)

1. *Gestion de l’ordinateur* → *Utilisateurs et groupes locaux* → **Utilisateurs** → **Nouvel utilisateur…**
2. Renseigne le profil → **Créer**.
3. Laisse-le tel quel (il reste *Users*).

## Créer un compte “support-admin” (Pro/Entreprise)

1. Crée l’utilisateur (comme ci-dessus).
2. *Groupes* → **Administrators** → **Ajouter…** → sélectionne le compte → **OK**.

## Désactiver “Guest” (Pro/Entreprise)

1. *Utilisateurs* → double-clique **Guest** → coche **Compte désactivé** → **OK**.

---

# Où cliquer si quelque chose “n’apparaît pas” ?

* Tu ne vois pas *Utilisateurs et groupes locaux* dans **Gestion de l’ordinateur** → tu es sûrement sur **Windows Home** (utilise la section Home ci-dessus).
* Le bouton **Changer le type de compte** est grisé → tu es connecté avec un compte **non administrateur** (connecte-toi avec un admin).
* Tu ne trouves pas **Panneau de configuration** → ouvre le **Menu Démarrer** et tape “Panneau de configuration”, puis choisis **Catégorie** → **Comptes d’utilisateurs**.


</details>















<details>
  <summary>☰ Méthode 2 - Windows Pro / Entreprise — Ajout d'utilisateurs avec les ligne de commandes </summary>

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

</details>
