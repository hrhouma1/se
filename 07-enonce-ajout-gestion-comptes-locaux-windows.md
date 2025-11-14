

# Énoncé — Ajout et gestion des comptes locaux sous Windows (Pro/Entreprise & Home)

## Sommaire

* [Objectif](#objectif)
* [Contexte](#contexte)
* [Compétences visées](#compétences-visées)
* [Prérequis & consignes](#prérequis--consignes)
* [Rappels sécurité & limites](#rappels-sécurité--limites)
* [Correction — Méthode 1 (GUI)](#correction--méthode-1--windows-pro--entreprise--tout-au-clic)
* [Windows Home — GUI](#windows-home--tout-au-clic-sans-utilisateurs-et-groupes-locaux)
* [Correction — Méthode 2 (CLI)](#correction--méthode-2--windows-pro--entreprise--lignes-de-commande)
* [Scénarios rapides](#scénarios-rapides)
* [Dépannage](#dépannage)
* [Fiche mémo](#fiche-mémo)



<details>
  <summary><strong>Objectif</strong></summary>

Mettre en place, pas à pas, la gestion des comptes <strong>locaux</strong> sous Windows 10/11 : création d’utilisateurs standard, élévation en administrateur, attribution de l’accès <strong>Bureau à distance (RDP)</strong>, réinitialisation/désactivation de comptes, et sécurisation des comptes intégrés. Deux approches sont proposées : <strong>tout au clic (GUI)</strong> et <strong>en ligne de commande (CMD/PowerShell)</strong>.

</details>

<details>
  <summary><strong>Contexte</strong></summary>

Dans un poste de travail ou un labo pédagogique, il est souvent nécessaire d’ajouter rapidement un compte “cours-prof” (standard), un compte “support-admin” (administrateur local), ou un compte “invité” temporaire, tout en respectant les bonnes pratiques de sécurité.

</details>

<details>
  <summary><strong>Compétences visées</strong></summary>

* Naviguer dans les consoles Windows (<em>Gestion de l’ordinateur</em>, <code>lusrmgr.msc</code>, <code>netplwiz</code>).
* Créer/administrer des comptes et des groupes locaux (Users, Administrators, Remote Desktop Users).
* Réaliser l’équivalent en <strong>CMD</strong> (<code>net.exe</code>) et <strong>PowerShell</strong> (module <strong>LocalAccounts</strong>).
* Appliquer des règles de sécurisation (désactivation de <em>Guest</em>, mots de passe robustes, journalisation).

</details>

<details>
  <summary><strong>Prérequis & consignes</strong></summary>

<strong>Prérequis</strong>

* Windows <strong>Pro/Entreprise/Éducation</strong> pour <code>lusrmgr.msc</code>.
* Windows <strong>Home</strong> : utiliser <strong>Paramètres</strong>, <strong>Panneau de configuration</strong>, <strong>CMD/PowerShell</strong> (pas de <code>lusrmgr.msc</code> ni d’hôte RDP).
* Session <strong>administrateur</strong> locale pour toutes les opérations système.

<strong>Consignes de réalisation</strong>

1. Choisir une méthode (Méthode 1 : GUI, Méthode 2 : CMD/PowerShell). Bonus si les deux.
2. Créer un compte standard <code>cours-prof</code> (ou équivalent) avec mot de passe.
3. Créer un admin local <code>support-admin</code> et l’ajouter à <strong>Administrators</strong>.
4. (Pro/Entreprise) Ajouter <code>cours-prof</code> à <strong>Remote Desktop Users</strong> et activer RDP dans Paramètres.
5. Sécuriser : désactiver <strong>Guest</strong> et laisser l’<strong>Administrator</strong> intégré désactivé si un autre admin existe.
6. Tester : connexion, changement/réinitialisation de mot de passe, désactivation/réactivation.
7. (CLI) Rejouer les opérations en <strong>PowerShell/CMD</strong> et conserver l’historique.

<strong>Checklist rapide</strong>

* [ ] <code>cours-prof</code> créé (groupe Users)
* [ ] <code>support-admin</code> créé (+ Administrators)
* [ ] (Pro/Entreprise) <code>cours-prof</code> ∈ Remote Desktop Users & RDP activé
* [ ] <em>Guest</em> désactivé, <em>Administrator</em> intégré non utilisé
* [ ] Captures/sorties et mini-rapport

</details>

<details>
  <summary><strong>Rappels sécurité & limites</strong></summary>

* Ne jamais publier de mots de passe en clair.
* Windows Home n’expose pas l’hôte RDP : utiliser <strong>Aide rapide</strong> ou un outil tiers.
* Activer la journalisation des échecs de connexion (Observateur d’événements → Security).

</details>

---

## Correction — Méthode 1 · Windows Pro / Entreprise — tout au clic

<details>
  <summary><strong>A. Ouvrir l’outil</strong></summary>

Menu Démarrer → “Gestion de l’ordinateur” → Outils système → Utilisateurs et groupes locaux.

</details>

<details>
  <summary><strong>B. Créer un utilisateur local (non admin)</strong></summary>

Utilisateurs → clic droit → Nouvel utilisateur… → Nom + mot de passe → décocher “Doit changer…” si mot de passe fixe → Créer → Fermer.

</details>

<details>
  <summary><strong>C. Passer un compte en administrateur</strong></summary>

Groupes → Administrators → Ajouter… → nom du compte → OK → Appliquer/OK.

</details>

<details>
  <summary><strong>D. Autoriser l’accès Bureau à distance (RDP)</strong></summary>

Groupes → Remote Desktop Users → Ajouter… → compte → OK → Appliquer/OK.
Activer RDP : Paramètres → Système → Bureau à distance.

</details>

<details>
  <summary><strong>E. Réinitialiser le mot de passe</strong></summary>

Utilisateurs → clic droit sur le compte → Définir le mot de passe… → Continuer → nouveau mot de passe → OK.

</details>

<details>
  <summary><strong>F. Désactiver / réactiver un compte</strong></summary>

Utilisateurs → double-clic compte → onglet Général → (dé)cocher Compte désactivé → OK.

</details>

<details>
  <summary><strong>G. Sécuriser les comptes intégrés</strong></summary>

<em>Guest</em> : Compte désactivé (recommandé). <em>Administrator</em> intégré : rester désactivé si un autre admin existe.

</details>



### Windows Home — tout au clic (sans “Utilisateurs et groupes locaux”)

<details>
  <summary><strong>A. Créer un compte local</strong></summary>

Paramètres → Comptes → Famille et autres utilisateurs → Ajouter un compte →
“Je ne dispose pas des informations de connexion” → “Ajouter un utilisateur sans compte Microsoft” → identifiants → Suivant.

</details>

<details>
  <summary><strong>B. Passer le compte en administrateur</strong></summary>

Paramètres → Comptes → Famille et autres utilisateurs → compte → Changer le type de compte → Administrateur → OK.

</details>

<details>
  <summary><strong>C. Changer le mot de passe d’un compte local</strong></summary>

Panneau de configuration → Comptes d’utilisateurs → Gérer un autre compte → sélectionner → Modifier le mot de passe.
Note : pas de rôle serveur RDP en Home.

</details>



## Correction — Méthode 2 · Windows Pro / Entreprise — lignes de commande

> Rappel : <code>lusrmgr.msc</code> est absent en Home. Utiliser PowerShell/CMD ou <code>netplwiz</code>.

<details>
  <summary><strong>Ouvrir l’outil (plusieurs méthodes)</strong></summary>

Direct : Win+R → <code>lusrmgr.msc</code> (Pro/Entreprise)
Gestion de l’ordinateur : Win+R → <code>compmgmt.msc</code>
CMD : <code>mmc.exe lusrmgr.msc</code>
PowerShell : <code>Start-Process lusrmgr.msc</code>
Universel : <code>netplwiz</code>, <code>control userpasswords2</code>, PowerShell/CMD

</details>

### CMD (net.exe) — fonctionne aussi en Home

```bat
:: Lister
net user
net localgroup
net localgroup administrators

:: Créer un compte
net user alice M0tDeP@ss! /add

:: Ajouter au groupe Administrators
net localgroup administrators alice /add

:: Désactiver / réactiver
net user alice /active:no
net user alice /active:yes

:: Forcer changement de mot de passe
net user alice /logonpasswordchg:yes

:: Mot de passe n’expire pas
net user alice /passwordchg:yes /passwordreq:yes
wmic useraccount where name='alice' set PasswordExpires=False

:: Supprimer
net user alice /del
```

### PowerShell (LocalAccounts) — recommandé

```powershell
# Lister
Get-LocalUser
Get-LocalGroup
Get-LocalGroupMember -Group "Administrators"

# Créer un utilisateur avec mot de passe sécurisé
$pwd = ConvertTo-SecureString 'M0tDeP@ss!' -AsPlainText -Force
New-LocalUser -Name 'alice' -FullName 'Alice Martin' -Password $pwd -PasswordNeverExpires:$false

# Ajouter aux groupes
Add-LocalGroupMember -Group 'Administrators' -Member 'alice'
Add-LocalGroupMember -Group 'Remote Desktop Users' -Member 'alice'

# Désactiver / réactiver
Disable-LocalUser -Name 'alice'
Enable-LocalUser  -Name 'alice'

# Réinitialiser le mot de passe
Set-LocalUser -Name 'alice' -Password $pwd

# Supprimer
Remove-LocalUser -Name 'alice'
```

## Scénarios rapides

<details>
  <summary><strong>Créer “cours-prof” (standard)</strong></summary>

GUI : Gestion de l’ordinateur → Utilisateurs → Nouvel utilisateur… → créer → rester dans Users.
PowerShell :

```powershell
$pwd = ConvertTo-SecureString 'N0uV3@u!' -AsPlainText -Force
New-LocalUser -Name 'cours-prof' -FullName 'Compte Cours Prof' -Password $pwd
```

</details>

<details>
  <summary><strong>Créer “support-admin” (admin local)</strong></summary>

```powershell
$pwd = ConvertTo-SecureString 'Adm1n-Urgence!' -AsPlainText -Force
New-LocalUser -Name 'support-admin' -Password $pwd
Add-LocalGroupMember -Group 'Administrators' -Member 'support-admin'
```

</details>

<details>
  <summary><strong>Compte temporaire (expire dans 7 jours)</strong></summary>

```powershell
$pwd = ConvertTo-SecureString 'Temp123!' -AsPlainText -Force
New-LocalUser -Name 'invite' -Password $pwd
Set-LocalUser -Name 'invite' -AccountExpires (Get-Date).AddDays(7)
```

</details>

<details>
  <summary><strong>Autoriser RDP</strong></summary>

```powershell
Add-LocalGroupMember -Group 'Remote Desktop Users' -Member 'cours-prof'
```

</details>



## Dépannage

* « Windows ne trouve pas <code>lusrmgr.msc</code> » : édition Home → CMD/PowerShell ou <code>netplwiz</code>.
* « Vous ne disposez pas des autorisations » : relancer en tant qu’administrateur.
* MMC vide à distance : pare-feu/ports DCOM/Remote Registry, droits admin distants.
* Échec d’ajout au groupe : orthographe compte/groupe, compte bien local (pas Microsoft/cloud).



## Fiche mémo

* Ouvrir : <code>lusrmgr.msc</code> | <code>compmgmt.msc</code> | <code>netplwiz</code>
* Lister : <code>Get-LocalUser</code> | <code>net user</code>
* Créer (PS) :

  ```powershell
  $pwd = ConvertTo-SecureString 'Xxx!' -AsPlainText -Force
  New-LocalUser -Name 'nom' -Password $pwd
  ```
* Admin : <code>Add-LocalGroupMember -Group 'Administrators' -Member 'nom'</code>
* Désactiver : <code>Disable-LocalUser -Name 'nom'</code>
* RDP : <code>Add-LocalGroupMember -Group 'Remote Desktop Users' -Member 'nom'</code>


**Astuce de mise en forme GitHub**

* Conserver le style sans icônes : utiliser <code><details></code>/<code><summary></code>, titres <code>#</code> clairs, blocs de code balisés (<code>bat</code>, <code>powershell</code>).
* Ajouter, si besoin, une section « Livrables » et une « Grille d’évaluation » à la fin du README pour les TP.











<details>
  <summary><strong>Annexe — Toutes les commandes</strong></summary>


## 

### 1) Lanceurs rapides (Win + R)

```text
lusrmgr.msc           # Utilisateurs et groupes locaux (Pro/Entreprise/Éducation uniquement)
compmgmt.msc          # Gestion de l’ordinateur
netplwiz              # Gestion simple des comptes locaux (toutes éditions)
control userpasswords2# Interface classique équivalente à netplwiz
eventvwr.msc          # Observateur d’événements (journaux Security)
mmc.exe lusrmgr.msc   # Ouvrir l’outil via la console MMC
```

> Remarque : `lusrmgr.msc` n’est pas disponible en édition Home.



### 2) CMD (net.exe) — fonctionne aussi en Home

#### 2.1 Lister comptes et groupes

```bat
net user
net localgroup
net localgroup administrators
net localgroup "Remote Desktop Users"
```

#### 2.2 Créer / gérer un utilisateur

```bat
net user alice M0tDeP@ss! /add
net user alice /active:no          & rem Désactiver
net user alice /active:yes         & rem Réactiver
net user alice /logonpasswordchg:yes  & rem Forcer changement mdp à la prochaine connexion
net user alice /del                & rem Supprimer l’utilisateur
```

#### 2.3 Ajouter / retirer des groupes

```bat
net localgroup administrators alice /add
net localgroup "Remote Desktop Users" alice /add
net localgroup administrators alice /delete
net localgroup "Remote Desktop Users" alice /delete
```

#### 2.4 Politiques de mot de passe (basique)

```bat
wmic useraccount where name='alice' set PasswordExpires=False
```

> Pour une gestion fine des stratégies, utiliser les stratégies locales (`secpol.msc`) ou des GPO (domaine).



### 3) PowerShell (module LocalAccounts) — recommandé

> Ouvrir **PowerShell en Administrateur**.

#### 3.1 Lister comptes et groupes

```powershell
Get-LocalUser
Get-LocalGroup
Get-LocalGroupMember -Group 'Administrators'
Get-LocalGroupMember -Group 'Remote Desktop Users'
```

#### 3.2 Créer un utilisateur avec mot de passe sécurisé

```powershell
$pwd = ConvertTo-SecureString 'M0tDeP@ss!' -AsPlainText -Force
New-LocalUser -Name 'alice' -FullName 'Alice Martin' -Password $pwd -PasswordNeverExpires:$false
```

#### 3.3 Définir / réinitialiser mot de passe

```powershell
$pwd = ConvertTo-SecureString 'N0uv3auM0tDeP@ss!' -AsPlainText -Force
Set-LocalUser -Name 'alice' -Password $pwd
```

#### 3.4 Activer / désactiver un compte

```powershell
Disable-LocalUser -Name 'alice'
Enable-LocalUser  -Name 'alice'
```

#### 3.5 Ajouter / retirer des groupes

```powershell
Add-LocalGroupMember -Group 'Administrators' -Member 'alice'
Add-LocalGroupMember -Group 'Remote Desktop Users' -Member 'alice'

Remove-LocalGroupMember -Group 'Administrators' -Member 'alice'
Remove-LocalGroupMember -Group 'Remote Desktop Users' -Member 'alice'
```

#### 3.6 Supprimer un utilisateur

```powershell
Remove-LocalUser -Name 'alice'
```



### 4) RDP (Bureau à distance) — attributions locales

> Côté **Pro/Entreprise/Éducation**.

#### 4.1 Ajouter un utilisateur au groupe RDP

```powershell
Add-LocalGroupMember -Group 'Remote Desktop Users' -Member 'alice'
```

#### 4.2 Vérifier appartenance

```powershell
Get-LocalGroupMember -Group 'Remote Desktop Users'
```

> L’hôte RDP n’existe pas en **Home** ; utiliser **Aide rapide** ou un outil tiers.

---

### 5) Gestion à distance (WinRM/PowerShell Remoting)

#### 5.1 Activer WinRM sur la machine distante (Admin)

```powershell
Enable-PSRemoting -Force
```

#### 5.2 Ouvrir une session distante depuis votre poste

```powershell
Enter-PSSession -ComputerName PC-CIBLE -Credential (Get-Credential)
# puis exécuter Get-LocalUser / New-LocalUser / Add-LocalGroupMember ... à distance
```

#### 5.3 Commandes ponctuelles sans session interactive

```powershell
Invoke-Command -ComputerName PC-CIBLE -Credential (Get-Credential) -ScriptBlock {
  $pwd = ConvertTo-SecureString 'Temp123!' -AsPlainText -Force
  New-LocalUser -Name 'invite' -Password $pwd
  Add-LocalGroupMember -Group 'Users' -Member 'invite'
}
```

---

### 6) Audit et journalisation (Observateur d’événements)

#### 6.1 Ouvrir les journaux

```text
eventvwr.msc
```

Naviguer : Journaux Windows → **Security**.
Événements utiles : connexions réussies/échouées, changements d’appartenance aux groupes, etc.
(Le cas échéant, activer l’audit dans la stratégie de sécurité locale ou via GPO.)



### 7) Modèles de commandes “réutilisables”

#### 7.1 Création standard (nom et mot de passe variables)

```powershell
# PowerShell
param(
  [string]$User = 'cours-prof',
  [string]$Pwd  = 'N0uV3@u!'
)
$secure = ConvertTo-SecureString $Pwd -AsPlainText -Force
New-LocalUser -Name $User -Password $secure
```

```bat
:: CMD
set USER=cours-prof
set PASS=N0uV3@u!
net user %USER% %PASS% /add
```

#### 7.2 Promotion en administrateur + RDP (si disponible)

```powershell
$u = 'support-admin'
$pwd = ConvertTo-SecureString 'Adm1n-Urgence!' -AsPlainText -Force
New-LocalUser -Name $u -Password $pwd
Add-LocalGroupMember -Group 'Administrators' -Member $u
Add-LocalGroupMember -Group 'Remote Desktop Users' -Member $u  # Pro/Entreprise
```

#### 7.3 Compte temporaire expirant dans 7 jours

```powershell
$pwd = ConvertTo-SecureString 'Temp123!' -AsPlainText -Force
New-LocalUser -Name 'invite' -Password $pwd
Set-LocalUser -Name 'invite' -AccountExpires (Get-Date).AddDays(7)
```

#### 7.4 Vérifications rapides

```powershell
Get-LocalUser | Format-Table Name,Enabled,LastLogon
Get-LocalGroupMember -Group 'Administrators'
Get-LocalGroupMember -Group 'Remote Desktop Users'
```



### 8) Points d’attention et équivalences

* **Forcer le changement de mot de passe à la prochaine connexion** :

  * CMD : `net user alice /logonpasswordchg:yes`
  * PowerShell (LocalAccounts) ne fournit pas de commutateur direct → utiliser `net.exe` ou une stratégie (GPO).

* **Expiration de mot de passe** :

  * Exemple basique CMD : `wmic useraccount where name='alice' set PasswordExpires=False`
  * Préférer les **stratégies** (locales/domaine) pour la conformité.

* **Édition Home** : pas de `lusrmgr.msc` ni d’hôte RDP. Utiliser **Paramètres**, **netplwiz**, **CMD/PowerShell** et **Aide rapide** pour l’assistance.



#### Annexe condensée (copier-coller rapide)

```bat
:: CMD — créer + admin + RDP
net user support-admin Adm1n-Urgence! /add
net localgroup administrators support-admin /add
net localgroup "Remote Desktop Users" support-admin /add
```

```powershell
# PowerShell — créer standard + vérifs
$pwd = ConvertTo-SecureString 'N0uV3@u!' -AsPlainText -Force
New-LocalUser -Name 'cours-prof' -FullName 'Compte Cours Prof' -Password $pwd
Get-LocalUser
Get-LocalGroupMember -Group 'Administrators'
```

---

> Intègre cette **Annexe — Toutes les commandes** en bas de ton README ; elle complète parfaitement tes sections GUI/CLI et sert de “cheat-sheet” exhaustive en TP.

</details>
