# Lab 1.3 — Inventaire et Documentation Système (Windows)

## Sommaire

* [Objectif](#objectif)
* [Contexte professionnel](#contexte-professionnel)
* [Compétences visées](#compétences-visées)
* [Prérequis & durée](#prérequis--durée)
* [Rappels sécurité & limites](#rappels-sécurité--limites)
* [Partie 1 — Informations système générales](#partie-1--informations-système-générales)
* [Partie 2 — Utilisateurs et groupes](#partie-2--utilisateurs-et-groupes)
* [Partie 3 — Processus en cours](#partie-3--processus-en-cours)
* [Partie 4 — Services Windows](#partie-4--services-windows)
* [Partie 5 — Connexions réseau actives](#partie-5--connexions-réseau-actives)
* [Partie 6 — Logiciels installés](#partie-6--logiciels-installés)
* [Partie 7 — Mises à jour et correctifs](#partie-7--mises-à-jour-et-correctifs)
* [Partie 8 — Configuration de sécurité](#partie-8--configuration-de-sécurité)
* [Partie 9 — Script d’inventaire automatisé](#partie-9--script-dinventaire-automatisé)
* [Partie 10 — Analyse & recommandations](#partie-10--analyse--recommandations)
* [Livrables](#livrables)
* [Critères d’évaluation](#critères-dévaluation)
* [Commandes de référence rapide](#commandes-de-référence-rapide)
* [Dépannage](#dépannage)
* [Aller plus loin (optionnel)](#aller-plus-loin-optionnel)
* [Ressources additionnelles](#ressources-additionnelles)
* [Fiche mémo](#fiche-mémo)
* [Annexe — Scripts & commandes](#annexe--scripts--commandes)

---

<details>
  <summary><strong>Objectif</strong></summary>

Collecter, structurer et documenter un inventaire complet d’une machine Windows 10/11 : informations OS et matériel, comptes et privilèges, processus et services, connexions réseau, logiciels et correctifs, paramètres de sécurité. Produire un rapport exploitable pour maintenance et sécurité.

</details>

<details>
  <summary><strong>Contexte professionnel</strong></summary>

L’inventaire système sert à documenter l’état de référence, détecter des changements, diagnostiquer des incidents, répondre aux audits et planifier mises à jour et migrations.

</details>

<details>
  <summary><strong>Compétences visées</strong></summary>

* Extraire des données système avec **CMD** et **PowerShell** (CIM/WMI).
* Cartographier comptes, privilèges, services, réseau.
* Générer un **rapport d’inventaire** reproductible et horodaté.
* Interpréter les résultats pour formuler des **recommandations**.

</details>

<details>
  <summary><strong>Prérequis & durée</strong></summary>

* Labs **1.1** et **1.2** complétés, droits **Administrateur**.
* Durée estimée : **30 minutes**.

</details>

<details>
  <summary><strong>Rappels sécurité & limites</strong></summary>

* L’inventaire peut contenir des données sensibles (noms, IP, logiciels). Stocker et partager **avec précaution**.
* **WMIC** est déprécié : privilégier **PowerShell (Get-CimInstance)**.
* N’auditer que des systèmes **autorisés**.

</details>

---

## Partie 1 — Informations système générales

<details>
  <summary><strong>Étape 1.1 — Vue d’ensemble</strong></summary>

Ouvrir **cmd** en Administrateur, puis :

```bat
systeminfo
```

Relever : Host Name, OS Name/Version (build), System Boot Time, Manufacturer/Model, Processeur(s), Total Physical Memory, Domaine/Groupe de travail.

**Questions**

1. Build exacte de Windows ?
2. Uptime depuis le dernier démarrage ?
3. Quantité de RAM ?
4. Domaine ou groupe de travail ?

</details>

<details>
  <summary><strong>Étape 1.2 — Export</strong></summary>

```bat
systeminfo > C:\Users\%USERNAME%\Desktop\inventaire_system.txt
```

</details>

<details>
  <summary><strong>Étape 1.3 — Matériel (PowerShell recommandé)</strong></summary>

Lancer **PowerShell** en Administrateur :

```powershell
Get-CimInstance Win32_Processor | Select-Object Name, NumberOfCores, NumberOfLogicalProcessors
Get-CimInstance Win32_PhysicalMemory | Select-Object Manufacturer, Capacity, Speed
Get-CimInstance Win32_DiskDrive | Select-Object Model, Size, Status
```

(Alternatives WMIC, si nécessaire) :

```bat
wmic cpu get name, numberofcores, numberoflogicalprocessors
wmic memorychip get capacity, speed, manufacturer
wmic diskdrive get model, size, status
```

**Questions**
5) Cœurs physiques/logiques ?
6) Vitesse RAM (MHz) ?
7) Taille totale du disque (bytes) ?

</details>

---

## Partie 2 — Utilisateurs et groupes

<details>
  <summary><strong>Étape 2.1 — Comptes locaux</strong></summary>

```bat
net user
net user %USERNAME%
```

Observer : date de création, expiration mot de passe, appartenance groupes, état du compte.

**Questions**
8) Combien d’utilisateurs locaux ?
9) Votre mot de passe expire-t-il ?
10) Des comptes inconnus ?

</details>

<details>
  <summary><strong>Étape 2.2 — Groupes locaux</strong></summary>

```bat
net localgroup
net localgroup Administrateurs
```

**Questions**
11) Quels groupes existent ?
12) Combien de membres dans **Administrateurs** ?

</details>

<details>
  <summary><strong>Étape 2.3 — Privilèges et SID</strong></summary>

```bat
whoami /all
```

Relever SID, groupes, privilèges, **Integrity Level**.

**Questions**
13) Votre **SID** ?
14) Quels **privilèges** ?
15) **Integrity Level** ?

</details>

---

## Partie 3 — Processus en cours

<details>
  <summary><strong>Étape 3.1 — Liste de base</strong></summary>

```bat
tasklist
```

**Questions**
16) Nombre total de processus ?
17) Quel processus a le **PID 4** ?

</details>

<details>
  <summary><strong>Étape 3.2 — Détails</strong></summary>

```bat
tasklist /svc
tasklist /v
```

</details>

<details>
  <summary><strong>Étape 3.3 — Recherche ciblée</strong></summary>

```bat
tasklist | findstr chrome
tasklist /v | findstr %USERNAME%
```

**Questions**
18) Nombre de `chrome.exe` actifs ?
19) Top 3 des processus par mémoire ?

</details>

---

## Partie 4 — Services Windows

<details>
  <summary><strong>Étape 4.1 — Lister</strong></summary>

```bat
sc query type= service state= all
sc query type= service state= running
```

</details>

<details>
  <summary><strong>Étape 4.2 — Détail d’un service</strong></summary>

```bat
sc qc WinDefend
sc query wuauserv
sc qc wuauserv
```

**Questions**
20) Combien de services en **running** ?
21) Windows Defender tourne-t-il ?
22) Type de démarrage de **wuauserv** ?

</details>

---

## Partie 5 — Connexions réseau actives

<details>
  <summary><strong>Étape 5.1 — Ports & sessions</strong></summary>

```bat
netstat -ano
netstat -anob
netstat -s
```

Interpréter : **LISTENING**, **ESTABLISHED**, **TIME_WAIT**.

**Questions**
23) Combien de connexions **ESTABLISHED** ?
24) Quel programme écoute sur **445** ?
25) Connexions vers des IP externes ?

</details>

---

## Partie 6 — Logiciels installés

<details>
  <summary><strong>Étape 6.1 — PowerShell (recommandé)</strong></summary>

```powershell
Get-ItemProperty HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\*,
                  HKLM:\Software\WOW6432Node\Microsoft\Windows\CurrentVersion\Uninstall\* `
| Select-Object DisplayName, DisplayVersion, Publisher | Sort-Object DisplayName
```

(Alternative lente WMIC) :

```bat
wmic product get name, version, vendor
```

**Questions**
26) Combien d’applications ?
27) Logiciels inconnus ?

</details>

---

## Partie 7 — Mises à jour et correctifs

<details>
  <summary><strong>Étape 7.1 — Hotfixes (KB)</strong></summary>

```bat
wmic qfe list brief
wmic qfe where "Description='Security Update'" get HotFixID, InstalledOn
```

PowerShell moderne :

```powershell
Get-CimInstance Win32_QuickFixEngineering | Select HotFixID, InstalledOn, Description | Sort-Object InstalledOn -Descending
```

**Questions**
28) Dernier correctif installé ?
29) Mises à jour < 30 jours ?

</details>

---

## Partie 8 — Configuration de sécurité

<details>
  <summary><strong>Étape 8.1 — Politique mots de passe</strong></summary>

```bat
net accounts
```

**Questions**
30) Longueur minimale ?
31) Seuil de verrouillage ?

</details>

<details>
  <summary><strong>Étape 8.2 — Pare-feu</strong></summary>

```bat
netsh advfirewall show allprofiles
```

**Questions**
32) Pare-feu actif pour tous les profils ?
33) Politique **Inbound** par défaut ?

</details>

<details>
  <summary><strong>Étape 8.3 — Windows Defender (PowerShell)</strong></summary>

```powershell
Get-MpComputerStatus | Select-Object AMServiceEnabled, AntispywareEnabled, RealTimeProtectionEnabled, AntivirusEnabled, FullScanAge, QuickScanAge, NISEnabled, AntispywareSignatureLastUpdated, AntivirusSignatureLastUpdated
```

**Questions**
34) Protection temps réel activée ?
35) Date de la dernière analyse complète ?

</details>

---

## Partie 9 — Script d’inventaire automatisé

<details>
  <summary><strong>Étape 9.1 — Batch minimal (CMD)</strong></summary>

Créer `inventaire.bat` :

```bat
@echo off
setlocal EnableDelayedExpansion
set OUT=inventaire_%COMPUTERNAME%_%DATE:~-4,4%%DATE:~-10,2%%DATE:~-7,2%_%TIME:~0,2%%TIME:~3,2%.txt
set OUT=%OUT: =0%

echo ======================================== > "%OUT%"
echo INVENTAIRE SYSTEME - %COMPUTERNAME% >> "%OUT%"
echo Date: %DATE% %TIME% >> "%OUT%"
echo ======================================== >> "%OUT%"

echo [SYSTEMINFO] >> "%OUT%"
systeminfo >> "%OUT%"

echo.>> "%OUT%"
echo [UTILISATEURS LOCAUX] >> "%OUT%"
net user >> "%OUT%"

echo.>> "%OUT%"
echo [GROUPES LOCAUX] >> "%OUT%"
net localgroup >> "%OUT%"

echo.>> "%OUT%"
echo [PROCESSUS ACTIFS] >> "%OUT%"
tasklist /v >> "%OUT%"

echo.>> "%OUT%"
echo [SERVICES EN COURS] >> "%OUT%"
sc query type= service state= running >> "%OUT%"

echo.>> "%OUT%"
echo [CONNEXIONS RESEAU] >> "%OUT%"
netstat -ano >> "%OUT%"

echo.>> "%OUT%"
echo [PARE-FEU] >> "%OUT%"
netsh advfirewall show allprofiles >> "%OUT%"

echo.>> "%OUT%"
echo [DERNIERES MISES A JOUR] >> "%OUT%"
wmic qfe list brief >> "%OUT%"

echo.>> "%OUT%"
echo Fichier genere: "%OUT%"
pause
```

Exécuter en **Administrateur**.

</details>

<details>
  <summary><strong>Étape 9.2 — Export horodaté</strong></summary>

```bat
inventaire.bat
```

Le nom de fichier est daté automatiquement (AAAAMMJJHHmm).

</details>

<details>
  <summary><strong>Étape 9.3 — Script PowerShell enrichi (HTML)</strong></summary>

Créer `Inventaire-HTML.ps1` :

```powershell
$now = Get-Date -Format "yyyyMMdd_HHmm"
$hostName = $env:COMPUTERNAME
$out = "$PSScriptRoot\inventaire_${hostName}_$now.html"

$sys = Get-CimInstance Win32_OperatingSystem
$cpu = Get-CimInstance Win32_Processor
$mem = Get-CimInstance Win32_PhysicalMemory
$disk = Get-CimInstance Win32_DiskDrive
$users = (net user) 2>$null
$groups = (net localgroup) 2>$null
$procs = Get-Process | Sort-Object -Property WS -Descending | Select-Object -First 25
$services = Get-Service | Where-Object {$_.Status -eq 'Running'} | Sort-Object DisplayName
$fw = (netsh advfirewall show allprofiles) 2>$null
$qfe = Get-CimInstance Win32_QuickFixEngineering | Sort-Object InstalledOn -Descending
$mp = Get-MpComputerStatus 2>$null

$ht = @"
<html><head>
<meta charset='utf-8'/>
<title>Inventaire $hostName</title>
<style>
body{font-family:Segoe UI,Roboto,Arial,sans-serif;line-height:1.5;color:#222;max-width:1000px;margin:2rem auto}
h1,h2{color:#2c3e50}
table{border-collapse:collapse;width:100%;margin:1rem 0}
th,td{border:1px solid #ddd;padding:.5rem;vertical-align:top}
th{background:#f5f5f5;text-align:left}
.code{background:#f9fbff;border:1px solid #dbe7ff;padding:.5rem;border-radius:.25rem;white-space:pre-wrap}
.small{font-size:.9rem;color:#555}
</style>
</head><body>
<h1>Inventaire système — $hostName</h1>
<p class="small">Généré le $(Get-Date)</p>

<h2>1) Système</h2>
<table>
<tr><th>OS</th><td>$($sys.Caption) $($sys.Version)</td></tr>
<tr><th>Boot Time</th><td>$([Management.ManagementDateTimeConverter]::ToDateTime($sys.LastBootUpTime))</td></tr>
<tr><th>CPU</th><td>$($cpu.Name) — Cores: $($cpu.NumberOfCores) / Threads: $($cpu.NumberOfLogicalProcessors)</td></tr>
<tr><th>RAM (slots)</th><td>$(@($mem | ForEach-Object { "$($_.Manufacturer) $([math]::Round($_.Capacity/1GB))GB @$($_.Speed)MHz"}) -join "<br/>")</td></tr>
<tr><th>Disques</th><td>$(@($disk | ForEach-Object { "$($_.Model) — $([math]::Round($_.Size/1GB)) GB — $($_.Status)" }) -join "<br/>")</td></tr>
</table>

<h2>2) Top 25 processus (mémoire)</h2>
<table><tr><th>Nom</th><th>PID</th><th>WS (MB)</th></tr>
$(@($procs | ForEach-Object { "<tr><td>$($_.ProcessName)</td><td>$($_.Id)</td><td>$([math]::Round($_.WS/1MB,1))</td></tr>" }) -join "`n")
</table>

<h2>3) Services actifs</h2>
<table><tr><th>Service</th><th>Nom</th><th>Statut</th></tr>
$(@($services | ForEach-Object { "<tr><td>$($_.DisplayName)</td><td>$($_.Name)</td><td>$($_.Status)</td></tr>" }) -join "`n")
</table>

<h2>4) Correctifs (KB) récents</h2>
<table><tr><th>KB</th><th>Date</th><th>Description</th></tr>
$(@($qfe | Select-Object -First 15 | ForEach-Object { "<tr><td>$($_.HotFixID)</td><td>$($_.InstalledOn)</td><td>$($_.Description)</td></tr>" }) -join "`n")
</table>

<h2>5) Windows Defender</h2>
<div class="code">$($mp | Out-String)</div>

<h2>6) Comptes & groupes (brut)</h2>
<div class="code"><strong>net user</strong>`n$users</div>
<div class="code"><strong>net localgroup</strong>`n$groups</div>

<h2>7) Pare-feu (profils)</h2>
<div class="code">$fw</div>

</body></html>
"@

$ht | Out-File -FilePath $out -Encoding utf8
Write-Host "Rapport HTML généré : $out"
```

Exécuter :

```powershell
powershell -ExecutionPolicy Bypass -File .\Inventaire-HTML.ps1
```

</details>

---

## Partie 10 — Analyse & recommandations

<details>
  <summary><strong>Étape 10.1 — Revue guidée</strong></summary>

**Sécurité** : services inutiles ? ports ouverts inattendus ? correctifs à jour ? pare-feu strict ?
**Performance** : processus gourmands ? mémoire libre suffisante ? démarrages automatiques inutiles ?
**Conformité** : politique mots de passe adéquate ? Defender actif/à jour ? comptes obsolètes ?

</details>

<details>
  <summary><strong>Étape 10.2 — Recommandations (3–5)</strong></summary>

Formuler des actions concrètes et justifiées pour **sécurité**, **performance**, **maintenance**.

</details>

---

## Livrables

* **Inventaire complet** (txt via batch ou HTML via PowerShell).
* **Document d’analyse** (2–3 pages) : réponses aux **35 questions**, tableau résumé, **Observations** & **Recommandations**.
* **Captures d’écran (≥5)** : `systeminfo`, `whoami /all`, `tasklist` tri mémoire, `netstat -ano`, pare-feu.

---

## Critères d’évaluation

| Critère                                        | Points |
| ---------------------------------------------- | :----: |
| Fichier d’inventaire complet et bien structuré |   25%  |
| Réponses aux questions (compréhension)         |   30%  |
| Analyse pertinente des informations collectées |   20%  |
| Recommandations réalistes et justifiées        |   15%  |
| Qualité de la documentation & captures         |   10%  |

---

## Commandes de référence rapide

**Informations système**

```bat
systeminfo
```

```powershell
Get-CimInstance Win32_OperatingSystem
Get-CimInstance Win32_Processor
Get-CimInstance Win32_PhysicalMemory
Get-CimInstance Win32_DiskDrive
```

**Utilisateurs & groupes**

```bat
net user
net user <username>
net localgroup
whoami /all
```

**Processus & services**

```bat
tasklist
tasklist /svc
sc query type= service
sc qc <service>
```

**Réseau**

```bat
netstat -ano
netstat -anob
netstat -s
```

**Sécurité**

```bat
net accounts
netsh advfirewall show allprofiles
wmic qfe list brief
```

```powershell
Get-CimInstance Win32_QuickFixEngineering
Get-MpComputerStatus
```

---

## Dépannage

**Accès refusé** : ouvrir **cmd/PowerShell** en Administrateur.
**WMIC indisponible** : utiliser **Get-CimInstance** (PowerShell).
**Script .bat n’exécute pas** : vérifier extension `.bat`, exécuter en Administrateur.
**PowerShell bloqué** : `powershell -ExecutionPolicy Bypass -File .\script.ps1`.

---

## Aller plus loin (optionnel)

* **PS avancé** : générer un rapport **HTML**/CSV/JUnit, archiver, pousser vers un partage sécurisé.
* **Comparaison temporelle** : exécuter chaque semaine, diff des rapports.
* **Audit conformité** : CIS-CAT, Microsoft Security Compliance Toolkit.

---

## Ressources additionnelles

* Microsoft Docs — CIM/WMI & PowerShell
* Net commands reference (`net user`, `net localgroup`)
* Windows Defender Cmdlets (`Get-MpComputerStatus`)
* CIS Benchmarks Windows

---

## Fiche mémo

* Préférer **PowerShell + CIM** pour la pérennité.
* Toujours **horodater** les exports.
* Conserver une **baseline** propre pour comparer.
* Séparer **collecte** (scripts) et **analyse** (rapport).

---

## Annexe — Scripts & commandes

<details>
  <summary><strong>A) Batch d’inventaire rapide (copier-coller)</strong></summary>

```bat
@echo off
setlocal
set OUT=inventaire_%COMPUTERNAME%_%DATE:~-4,4%%DATE:~-10,2%%DATE:~-7,2%_%TIME:~0,2%%TIME:~3,2%.txt
set OUT=%OUT: =0%
(
echo [SYSTEMINFO]
systeminfo
echo.
echo [USERS]
net user
echo.
echo [GROUPS]
net localgroup
echo.
echo [TOP PROCESS]
tasklist /v
echo.
echo [SERVICES RUNNING]
sc query type= service state= running
echo.
echo [NETSTAT]
netstat -ano
echo.
echo [FIREWALL]
netsh advfirewall show allprofiles
echo.
echo [QFE]
wmic qfe list brief
) > "%OUT%"
echo Fichier: %OUT%
```

</details>

<details>
  <summary><strong>B) PowerShell — Listing logiciels (x64+x86)</strong></summary>

```powershell
Get-ItemProperty HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\*,
                  HKLM:\Software\WOW6432Node\Microsoft\Windows\CurrentVersion\Uninstall\* `
| Select DisplayName, DisplayVersion, Publisher | Sort DisplayName
```

</details>

<details>
  <summary><strong>C) PowerShell — Top mémoire</strong></summary>

```powershell
Get-Process | Sort-Object WS -Descending | Select-Object -First 20 Name,Id,@{n='WS_MB';e={[math]::Round($_.WS/1MB,1)}}
```

</details>

<details>
  <summary><strong>D) PowerShell — Defender résumé</strong></summary>

```powershell
Get-MpComputerStatus | Select AMServiceEnabled,AntivirusEnabled,RealTimeProtectionEnabled,AntivirusSignatureLastUpdated,FullScanAge
```

</details>

<details>
  <summary><strong>E) PowerShell — Rapport HTML (modèle minimal)</strong></summary>

```powershell
# Voir script complet dans la Partie 9.3
```

</details>
