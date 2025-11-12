# Lab 1.4 — Gestion et Sécurité des Credentials Wi-Fi (Windows)

## Sommaire

* [Objectif](#objectif)
* [Contexte professionnel](#contexte-professionnel)
* [Compétences visées](#compétences-visées)
* [Prérequis & durée](#prérequis--durée)
* [Avertissement éthique & légal (IMPORTANT)](#avertissement-éthique--légal-important)
* [Partie 1 — Comprendre le stockage des credentials Wi-Fi](#partie-1--comprendre-le-stockage-des-credentials-wi-fi)
* [Partie 2 — Lister les profils Wi-Fi enregistrés](#partie-2--lister-les-profils-wi-fi-enregistrés)
* [Partie 3 — Extraction des mots de passe (audit)](#partie-3--extraction-des-mots-de-passe-audit)
* [Partie 4 — Export & implications de sécurité](#partie-4--export--implications-de-sécurité)
* [Partie 5 — Scan des réseaux à portée](#partie-5--scan-des-réseaux-à-portée)
* [Partie 6 — Gestion sécurisée des profils](#partie-6--gestion-sécurisée-des-profils)
* [Partie 7 — Mesures de protection (utilisateur & admin)](#partie-7--mesures-de-protection-utilisateur--admin)
* [Partie 8 — Analyse de risque — Scénarios](#partie-8--analyse-de-risque--scénarios)
* [Partie 9 — Lab pratique : script d’audit](#partie-9--lab-pratique--script-daudit)
* [Livrables](#livrables)
* [Critères d’évaluation](#critères-dévaluation)
* [Commandes de référence rapide](#commandes-de-référence-rapide)
* [Dépannage](#dépannage)
* [Aller plus loin (optionnel)](#aller-plus-loin-optionnel)
* [Ressources additionnelles](#ressources-additionnelles)
* [Conclusion & engagement éthique](#conclusion--engagement-éthique)

---

<details>
  <summary><strong>Objectif</strong></summary>

Auditer et sécuriser les **credentials Wi-Fi** sous Windows : inventorier les profils enregistrés, comprendre où et comment sont stockés les mots de passe, évaluer les risques (accès physique, malwares), appliquer des contre-mesures (suppression de profils, chiffrement, politiques).

</details>

<details>
  <summary><strong>Contexte professionnel</strong></summary>

En entreprise, l’exposition des mots de passe Wi-Fi via des profils stockés localement constitue un **risque** (perte/vol, forensics, malwares avec privilèges). Les administrateurs doivent **auditer**, **documenter** et **réduire** cette surface d’attaque.

</details>

<details>
  <summary><strong>Compétences visées</strong></summary>

* Lister et analyser les **profils WLAN**.
* Extraire les **paramètres de sécurité** (authentification, chiffrement).
* Évaluer et **mitiger** les risques (suppression, désactivation auto-join, chiffrement du disque, GPO).
* Produire un **rapport d’audit** opérationnel.

</details>

<details>
  <summary><strong>Prérequis & durée</strong></summary>

* Labs **1.1, 1.2, 1.3** complétés ; droits **Administrateur**.
* PC Windows avec Wi-Fi (ou historique de connexions).
* Durée estimée : **25 minutes**.

</details>

<details>
  <summary><strong>Avertissement éthique & légal (IMPORTANT)</strong></summary>

AUTORISÉ : votre poste, systèmes dont vous êtes propriétaire/administrateur, lab du cours, **avec autorisation écrite**.
INTERDIT : postes tiers sans consentement, systèmes d’entreprise sans mandat, accès non autorisé à des réseaux.
Conséquences possibles (Canada) : **poursuites**, **amendes**, **emprisonnement**, **expulsion**.
Vous vous engagez à un usage **responsable** et **légal**.

</details>

---

## Partie 1 — Comprendre le stockage des credentials Wi-Fi

<details>
  <summary><strong>Étape 1.1 — Profil réseau & emplacements</strong></summary>

Un **profil WLAN** contient : **SSID**, type de sécurité (WPA2/WPA3), clé, auto-connexion.
Stockage : registre + fichiers XML chiffrés sous :

```
C:\ProgramData\Microsoft\Wlansvc\Profiles\Interfaces\
```

**Questions** :

1. Pourquoi Windows stocke-t-il les mots de passe ?
2. Avantages / inconvénients ?

</details>

<details>
  <summary><strong>Étape 1.2 — Implications de sécurité</strong></summary>

Risques : accès physique, malware admin, vol/perte, persistance forensics.
Principe : **plus il y a de profils**, plus la surface d’attaque augmente.

</details>

---

## Partie 2 — Lister les profils Wi-Fi enregistrés

<details>
  <summary><strong>Étape 2.1 — Inventaire</strong></summary>

```bat
netsh wlan show profiles
```

Observez tous les réseaux (domicile, travail, publics).
**Questions** : 3) Combien de profils ? 4) Tous reconnus ? 5) Profils obsolètes ?

</details>

<details>
  <summary><strong>Étape 2.2 — Qualification</strong></summary>

Classer : **de confiance** (maison, travail) vs **publics** (cafés, hôtels) vs **inconnus/obsolètes**.
Bonne pratique : **supprimer** ce qui n’est plus nécessaire.

</details>

---

## Partie 3 — Extraction des mots de passe (audit)

> Nécessite **Invite de commandes (Admin)**.

<details>
  <summary><strong>Étape 3.1 — Détail d’un profil (avec clé)</strong></summary>

```bat
netsh wlan show profile name="MON_WIFI" key=clear
```

Repérer **Contenu de la clé** (Key Content), type d’authentification, chiffrement, auto-connexion.
**Questions** : 6) Récupération OK ? 7) Mot de passe robuste ? 8) Votre avis sur cette possibilité ?

</details>

<details>
  <summary><strong>Étape 3.2 — Comparaison multi-profils</strong></summary>

```bat
netsh wlan show profile name="Starbucks_WiFi" key=clear
netsh wlan show profile name="Bureau_Secure" key=clear
```

**Questions** : 9) Sécurité de votre réseau principal ? 10) Auto-connexion activée ? 11) Auto-join public = bonne idée ?
12) Complexité des mots de passe publics ? 13) Différences de posture sécurité ?

</details>

---

## Partie 4 — Export & implications de sécurité

<details>
  <summary><strong>Étape 4.1 — Export XML (avec clés)</strong></summary>

```bat
mkdir C:\Temp\WiFi_Export
netsh wlan export profile key=clear folder="C:\Temp\WiFi_Export"
explorer C:\Temp\WiFi_Export
```

Ouvrez un XML → balise `<keyMaterial>` (la clé en clair).
**Questions** : 14) Nombre de fichiers ? 15) Mot de passe en clair ? 16) Impact si copie USB ?

</details>

<details>
  <summary><strong>Étape 4.2 — Scénario d’attaque (physique)</strong></summary>

Accès non verrouillé (<60s) → **export** → vol des XML.
**Questions** : 17) Contre-mesures ? 18) Pourquoi verrouiller immédiatement sa session ?

</details>

---

## Partie 5 — Scan des réseaux à portée

<details>
  <summary><strong>Étape 5.1 — Découverte</strong></summary>

```bat
netsh wlan show networks mode=bssid
```

Observez : SSID, Authentification (WPA2/WPA3/Ouvert), Chiffrement, Signal, BSSID, Canal.
**Questions** : 19) Réseaux visibles ? 20) Combien **Ouverts** ? 21) WEP présent ?

</details>

<details>
  <summary><strong>Étape 5.2 — Tableau rapide</strong></summary>

| SSID        | Sécurité | Signal | Sécurisé ?         |
| ----------- | -------- | -----: | ------------------ |
| MonWiFi_5G  | WPA2     |   100% | Oui                |
| Cafe_Public | Ouvert   |    75% | Non                |
| Voisin123   | WEP      |    50% | **Non (obsolète)** |

**Questions** : 22) % de réseaux ouverts ? 23) Vous connecteriez-vous à un réseau ouvert ? Pourquoi ?

</details>

<details>
  <summary><strong>Étape 5.3 — Interface active</strong></summary>

```bat
netsh wlan show interfaces
```

Relever : SSID courant, BSSID, 802.11n/ac/ax, canal, débit, signal.
**Questions** : 24) Réseau actuel ? 25) Signal ? 26) Standard Wi-Fi ?

</details>

---

## Partie 6 — Gestion sécurisée des profils

<details>
  <summary><strong>Étape 6.1 — Suppression des profils obsolètes</strong></summary>

```bat
netsh wlan delete profile name="VieuxReseau"
```

**Questions** : 27) Pourquoi supprimer ? 28) Combien supprimés ?

</details>

<details>
  <summary><strong>Étape 6.2 — Désactiver l’auto-connexion (publics)</strong></summary>

Paramètres → Réseau & Internet → Wi-Fi → **Gérer les réseaux connus** → réseau public → **désactiver** *Se connecter automatiquement*.
**Questions** : 29) Qui doit garder l’auto-connexion ? 30) Qui ne doit pas ?

</details>

<details>
  <summary><strong>Étape 6.3 — Nettoyage des exports</strong></summary>

```bat
del /F /Q C:\Temp\WiFi_Export\*.xml
rmdir C:\Temp\WiFi_Export
```

Si transfert nécessaire : **clé USB chiffrée**, supprimer immédiatement après.

</details>

---

## Partie 7 — Mesures de protection (utilisateur & admin)

<details>
  <summary><strong>Étape 7.1 — Bonnes pratiques (utilisateur)</strong></summary>

* **Verrouillage auto** (1–2 min) ; **Win+L** manuellement.
* **Mot de passe Windows robuste** ; éviter PIN faibles.
* **BitLocker** (Windows Pro) pour chiffrer le disque.
* **Hygiène des profils** (audit/suppression réguliers).
* **Réseaux publics** : VPN, pas d’auto-join, oublier après usage.
  **Questions** : 31) Verrouillage auto activé ? 32) BitLocker activé (propriétés disque C:) ?

</details>

<details>
  <summary><strong>Étape 7.2 — Bonnes pratiques (administrateur)</strong></summary>

* **GPO** : restreindre `netsh` aux non-admins, imposer BitLocker.
* **EDR/Antivirus** : surveiller export de profils & dumps credentials.
* **Audit** : journaliser connexions Wi-Fi, alertes anomalies.
* **Sensibilisation** : sécurité **physique** & verrouillage de session.

</details>

<details>
  <summary><strong>Étape 7.3 — Checklist personnelle</strong></summary>

* [ ] Mot de passe Windows complexe et unique
* [ ] Verrouillage automatique ≤ 2 min
* [ ] Disque système chiffré (BitLocker)
* [ ] Profils Wi-Fi obsolètes supprimés
* [ ] Auto-connexion désactivée pour réseaux publics
* [ ] Antivirus/EDR à jour
* [ ] Pare-feu Windows actif
  **Questions** : 33) Points validés ? 34) Points faibles ?

</details>

---

## Partie 8 — Analyse de risque — Scénarios

<details>
  <summary><strong>Scénario 1 — Vol de portable non chiffré</strong></summary>

**Questions** : 35) Infos accessibles ? 36) Réseaux désormais utilisables par l’attaquant ? 37) Actions immédiates ?
**Actions recommandées** : changer mots de passe Wi-Fi & comptes critiques, plainte, informer l’employeur, révocation accès.

</details>

<details>
  <summary><strong>Scénario 2 — Accès physique 2 minutes</strong></summary>

**Questions** : 38) Faisable ? 39) Détection ? 40) Prévention ?

</details>

<details>
  <summary><strong>Scénario 3 — Malware avec privilèges admin</strong></summary>

**Questions** : 41) Escalade de privilèges : comment ? 42) Protections ? 43) Outils de détection ?

</details>

---

## Partie 9 — Lab pratique : script d’audit

<details>
  <summary><strong>Étape 9.1 — Script batch (audit_wifi.bat)</strong></summary>

```bat
@echo off
echo ============================================
echo AUDIT DE SECURITE WI-FI
echo Date: %DATE% %TIME%
echo Ordinateur: %COMPUTERNAME%
echo Utilisateur: %USERNAME%
echo ============================================
echo.

echo [1] PROFILS WI-FI ENREGISTRES
netsh wlan show profiles
echo.

echo [2] INTERFACE WI-FI ACTUELLE
netsh wlan show interfaces
echo.

echo [3] RESEAUX A PORTEE (BSSID)
netsh wlan show networks mode=bssid
echo.

echo [4] DETAILS DU PROFIL ACTUEL (sans mot de passe)
for /f "tokens=2,* delims=:" %%a in ('netsh wlan show interfaces ^| findstr /R /C:"^\ *SSID"') do (
  set "current_ssid=%%b"
)
set current_ssid=%current_ssid:~1%
if not "%current_ssid%"=="" netsh wlan show profile name="%current_ssid%"
echo.

echo RECOMMANDATIONS:
echo - Supprimez les profils obsoletes
echo - Desactivez l'auto-connexion pour les reseaux publics
echo - Activez BitLocker et le verrouillage automatique
echo ============================================
```

Exécuter et sauvegarder :

```bat
audit_wifi.bat > audit_wifi_report.txt
```

</details>

<details>
  <summary><strong>Étape 9.2 — Analyse guidée</strong></summary>

**Profils** : obsolètes = ___ ; à supprimer = ___.
**Sécurité** : WPA2/WPA3 = ___ ; ouverts/WEP = ___.
**Configuration** : signal = ___ ; sécurité du réseau courant = ___ ; auto-connexion = ___.

</details>

<details>
  <summary><strong>Étape 9.3 — Plan d’action (5 mesures)</strong></summary>

Lister 5 actions concrètes (ex. suppression X profils, activer BitLocker, GPO `netsh`, VPN public, verrouillage auto 1 min).

</details>

---

## Livrables

* **audit_wifi_report.txt** (script).
* **Analyse 3–4 pages** : réponses aux **43 questions**, tableau des profils & posture, scénarios traités, **plan d’action (5 points)**, **checklist** complétée.
* **Captures (≥4)** :

  1. `netsh wlan show profiles`
  2. extraction d’un mot de passe (**flouté**)
  3. scan BSSID
  4. dossier d’export (avant suppression)

> **Masquez/Floutez** les secrets réels dans toutes les captures.

---

## Critères d’évaluation

| Critère                                             | Points |
| --------------------------------------------------- | -----: |
| Compréhension des risques (stockage credentials)    |    25% |
| Qualité de l’audit (identification des problèmes)   |    25% |
| Analyse des scénarios d’attaque                     |    20% |
| Plan d’action réaliste & applicable                 |    15% |
| Qualité de la documentation & respect des consignes |    15% |

---

## Commandes de référence rapide

**Gestion des profils**

```bat
netsh wlan show profiles
netsh wlan show profile name="SSID"
netsh wlan show profile name="SSID" key=clear
netsh wlan delete profile name="SSID"
netsh wlan export profile key=clear folder="C:\Path"
```

**Scan & informations**

```bat
netsh wlan show networks mode=bssid
netsh wlan show interfaces
netsh wlan show drivers
```

**Connexion**

```bat
netsh wlan connect name="SSID"
netsh wlan disconnect
```

**Sécurité disque (GUI/PowerShell, indicatif)**

```powershell
# Vérifier l'état BitLocker (si Pro/Entreprise)
Manage-bde -status
```

---

## Dépannage

**Service WLAN non démarré**

```bat
net start WlanSvc
```

**Accès refusé (clé en clair)** : ouvrir l’Invite de commandes **en Administrateur**.

**Pas d’adaptateur Wi-Fi** : commandes WLAN inopérantes (PC fixe sans Wi-Fi).

**Export générant des XML** : ne **pas** conserver ; **supprimer** après usage :

```bat
del /F /Q C:\Temp\WiFi_Export\*.xml && rmdir C:\Temp\WiFi_Export
```

---

## Aller plus loin (optionnel)

* **PowerShell avancé** : lister tous les profils, détecter >6 mois d’inactivité, proposer suppression, générer un **rapport HTML/PDF**.
* **Comparaison Linux** : `/etc/NetworkManager/system-connections/` (root), comparer les modèles de sécurité.
* **Recherche** : chiffrement au repos, API/DPAPI utilisée, pistes d’endurcissement.

---

## Ressources additionnelles

* Documentation **netsh wlan** (Microsoft)
* Wi-Fi Security : WEP, WPA2, WPA3
* NIST — Wireless Security Guidelines
* **BitLocker** — documentation Microsoft

---

## Conclusion & engagement éthique

Vous savez **auditer** et **protéger** les credentials Wi-Fi sous Windows : inventaire des profils, extraction contrôlée, export & risques, gestion sécurisée, politiques et chiffrement.
Clés de réussite : **sécurité physique**, **moindre privilège**, **défense en profondeur**, **audits réguliers**.

> **Engagement**
> « Je, ____________, m’engage à utiliser ces connaissances de manière responsable, avec autorisation explicite, dans le respect de la loi et de la vie privée. »

*Lab développé dans le cadre du cours Systèmes d’Exploitation — MODULE 1.*
