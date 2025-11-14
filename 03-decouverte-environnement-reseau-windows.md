# Lab 1.1 — Découverte de l’Environnement Réseau (Windows)

## Sommaire

* [Objectif](#objectif)
* [Contexte professionnel](#contexte-professionnel)
* [Compétences visées](#compétences-visées)
* [Prérequis & consignes](#prérequis--consignes)
* [Durée estimée](#durée-estimée)
* [Rappels sécurité & limites](#rappels-sécurité--limites)
* [Partie 1 — Identification de la configuration réseau](#partie-1--identification-de-la-configuration-réseau)
* [Partie 2 — Tests de connectivité](#partie-2--tests-de-connectivité)
* [Partie 3 — Traçage du chemin réseau](#partie-3--traçage-du-chemin-réseau)
* [Partie 4 — Documentation de l’environnement](#partie-4--documentation-de-lenvironnement)
* [Exercices supplémentaires (optionnels)](#exercices-supplémentaires-optionnels)
* [Livrables](#livrables)
* [Critères d’évaluation](#critères-dévaluation)
* [Commandes de référence rapide](#commandes-de-référence-rapide)
* [Dépannage](#dépannage)
* [Prochaines étapes](#prochaines-étapes)
* [Ressources additionnelles](#ressources-additionnelles)
* [Fiche mémo](#fiche-mémo)
* [Annexe — Toutes les commandes](#annexe--toutes-les-commandes)

---

<details>
  <summary><strong>Objectif</strong></summary>

Découvrir et documenter la configuration réseau d’un poste Windows, diagnostiquer les problèmes de base, analyser le chemin des paquets et produire un rapport technique concis.

</details>

<details>
  <summary><strong>Contexte professionnel</strong></summary>

Ces commandes et méthodes sont utilisées quotidiennement par les administrateurs systèmes et techniciens réseau pour diagnostiquer la connectivité, valider la configuration, documenter l’infrastructure et repérer les anomalies de routage ou de DNS.

</details>

<details>
  <summary><strong>Compétences visées</strong></summary>

* Identifier adresses IP, masque, passerelle, DNS, MAC.
* Tester la connectivité locale et Internet via `ping`.
* Visualiser le routage vers une cible via `tracert`.
* Capturer et archiver les informations de diagnostic.

</details>

<details>
  <summary><strong>Prérequis & consignes</strong></summary>

<strong>Prérequis</strong>

* Windows 10/11 avec accès à l’Invite de commandes.
* Connexion Internet active.

<strong>Consignes</strong>

1. Exécuter les commandes depuis **Invite de commandes (cmd)**.
2. Nommer clairement vos fichiers de sortie.
3. Répondre aux questions numérotées dans votre document de réponses.

</details>

<details>
  <summary><strong>Durée estimée</strong></summary>

20 minutes.

</details>

<details>
  <summary><strong>Rappels sécurité & limites</strong></summary>

* N’exécuter ces commandes que sur des systèmes que vous administrez ou pour lesquels vous avez une autorisation explicite.
* Le pare-feu local, celui du routeur ou du FAI peut bloquer des pings (ICMP).
* Les adresses publiques vues dans `tracert` ne révèlent pas nécessairement la topologie interne complète des opérateurs.

</details>

---

## Partie 1 — Identification de la configuration réseau

<details>
  <summary><strong>Étape 1.1 — Ouvrir l’invite de commandes</strong></summary>

Windows + R → `cmd` → Entrée.

</details>

<details>
  <summary><strong>Étape 1.2 — Commande <code>ipconfig</code></strong></summary>

```bat
ipconfig
```

Notez : **IPv4**, **Masque de sous-réseau**, **Passerelle par défaut**.

**Questions**

1. Combien d’adaptateurs réseau sont listés ?
2. Quelle est votre adresse IP actuelle ?
3. Quelle est l’adresse de votre passerelle par défaut ?

</details>

<details>
  <summary><strong>Étape 1.3 — Détails complets <code>ipconfig /all</code></strong></summary>

```bat
ipconfig /all
```

Notez : **Adresse MAC**, **Serveurs DNS**, **DHCP activé (Oui/Non)**, **Nom d’hôte**.

**Questions**
4) Votre IP est-elle attribuée par DHCP ou statique ?
5) Quels serveurs DNS sont configurés ?
6) Quelle est votre adresse MAC (adresse physique) ?

</details>

---

## Partie 2 — Tests de connectivité

<details>
  <summary><strong>Étape 2.1 — Ping de la passerelle</strong></summary>

Remplacez `X.X.X.X` par **votre** passerelle.

```bat
ping 192.168.X.X
```

Observez : temps moyen, paquets envoyés/reçus, pertes.

**Questions**
7) Recevez-vous des réponses ?
8) Quel est le temps moyen (ms) ?

</details>

<details>
  <summary><strong>Étape 2.2 — Ping d’un site Internet</strong></summary>

```bat
ping google.com
```

Observez : résolution DNS, latence généralement > ping passerelle.

**Questions**
9) Quelle adresse IP est résolue pour `google.com` ?
10) Pourquoi le temps de réponse est-il plus élevé que pour la passerelle ?

</details>

<details>
  <summary><strong>Étape 2.3 — Ping continu (surveillance courte)</strong></summary>

```bat
ping google.com -t
```

Laissez tourner ~30 s, puis **Ctrl + C**.

**Question**
11) Avez-vous observé des pertes de paquets ?

</details>

---

## Partie 3 — Traçage du chemin réseau

<details>
  <summary><strong>Étape 3.1 — <code>tracert</code> vers Google</strong></summary>

```bat
tracert google.com
```

Observez : nombre de sauts, premier saut (souvent passerelle), éventuels « Délai d’attente dépassé ».

**Questions**
12) Combien de sauts pour atteindre `google.com` ?
13) Quel est le premier saut ? Est-ce votre passerelle ?
14) À partir de quel saut quittez-vous le réseau local (LAN) ?

</details>

<details>
  <summary><strong>Étape 3.2 — Autre cible (ex. Amazon Canada)</strong></summary>

```bat
tracert amazon.ca
```

**Questions**
15) Y a-t-il plus ou moins de sauts que pour `google.com` ?
16) Identifiez-vous des routeurs appartenant à votre FAI ?

</details>

---

## Partie 4 — Documentation de l’environnement

<details>
  <summary><strong>Étape 4.1 — Créer un rapport</strong></summary>

Rédigez un document contenant :

**Configuration réseau**

* Adresse IP :
* Masque de sous-réseau :
* Passerelle par défaut :
* Serveurs DNS :
* Adresse MAC :
* DHCP activé : Oui / Non

**Tests de connectivité**

* Ping passerelle : Succès/Échec — Temps moyen : ___ ms
* Ping google.com : Succès/Échec — Temps moyen : ___ ms
* Sauts vers google.com : ___

</details>

<details>
  <summary><strong>Étape 4.2 — Capturer les informations</strong></summary>

```bat
ipconfig /all > C:\Users\%USERNAME%\Desktop\config_reseau.txt
```

Le fichier est généré sur le Bureau.

</details>

---

## Exercices supplémentaires (optionnels)

<details>
  <summary><strong>Exercice 1 — Méthode de dépannage</strong></summary>

Ordonnancer : `ipconfig` → ping passerelle → ping 8.8.8.8 → ping `google.com` → `tracert`.
But : isoler un défaut local, de routage ou DNS.

</details>

<details>
  <summary><strong>Exercice 2 — Comparaison de chemins</strong></summary>

Comparer `tracert` vers `google.com`, `facebook.com`, `wikipedia.org`.
Où les chemins divergent-ils ?

</details>

<details>
  <summary><strong>Exercice 3 — Dossier professionnel</strong></summary>

Word/PDF : titre, nom, date, captures (`ipconfig /all`, `ping`, `tracert`), réponses 1–16, conclusion sur l’état de la connectivité.

</details>

---

## Livrables

* Fichier texte généré par `ipconfig /all`.
* Document de réponses avec toutes les questions (1–16).
* Captures d’écran : `ipconfig /all`, `ping google.com`, `tracert google.com`.

---

## Critères d’évaluation

| Critère                                            | Points |
| -------------------------------------------------- | :----: |
| Configuration réseau correctement documentée       |   30%  |
| Tests de connectivité exécutés et documentés       |   30%  |
| Traçage analysé avec identification des sauts      |   25%  |
| Qualité de la documentation (clarté, organisation) |   15%  |

---

## Commandes de référence rapide

| Commande            | Description                            | Exemple              |
| ------------------- | -------------------------------------- | -------------------- |
| `ipconfig`          | Affiche la configuration IP de base    | `ipconfig`           |
| `ipconfig /all`     | Affiche toutes les informations réseau | `ipconfig /all`      |
| `ping <adresse>`    | Teste la connectivité ICMP             | `ping google.com`    |
| `ping <adresse> -t` | Ping continu (Ctrl+C pour arrêter)     | `ping 8.8.8.8 -t`    |
| `tracert <adresse>` | Trace le chemin réseau (hops)          | `tracert google.com` |
| `ipconfig /release` | Libère l’IP DHCP                       | `ipconfig /release`  |
| `ipconfig /renew`   | Renouvelle l’IP DHCP                   | `ipconfig /renew`    |

---

## Dépannage

**Problème :** « La commande n’est pas reconnue »
**Solution :** Ouvrez **Invite de commandes (cmd)**, pas PowerShell. Vérifiez l’orthographe.

**Problème :** « Délai d’attente de la demande dépassé »
**Solution :** Vérifiez la liaison réseau, ping passerelle, pare-feu local/routeur/FAI pouvant bloquer ICMP.

**Problème :** « Impossible de résoudre l’adresse »
**Solution :** Suspect DNS. Vérifiez `ipconfig /all`, essayez `ping 8.8.8.8`. Si IP répond mais pas nom de domaine, corrigez la config DNS.

---

## Prochaines étapes

* Utiliser **Wireshark** pour observer le trafic.
* Étudier les protocoles (TCP, UDP, DNS, HTTP).
* Analyser des paquets capturés et interpréter les champs clés.

---

## Ressources additionnelles

* Documentation Microsoft — `ipconfig`
* Documentation Microsoft — `ping`
* Documentation Microsoft — `tracert`

---

## Fiche mémo

* Voir IP/Passerelle/DNS : `ipconfig /all`
* Test local (passerelle) : `ping <gateway>`
* Test Internet : `ping 8.8.8.8` puis `ping domaine`
* Traceroute : `tracert domaine`
* Renouveler DHCP : `ipconfig /release` ; `ipconfig /renew`
* Exporter la config : `ipconfig /all > %USERPROFILE%\Desktop\config_reseau.txt`

---

## Annexe — Toutes les commandes

<details>
  <summary><strong>1) Identification & IP</strong></summary>

```bat
:: Vue rapide
ipconfig

:: Vue complète (inclut MAC, DNS, DHCP)
ipconfig /all

:: Libérer / Renouveler (DHCP)
ipconfig /release
ipconfig /renew
```

</details>

<details>
  <summary><strong>2) Tests ICMP (connectivité)</strong></summary>

```bat
:: Ping passerelle (remplacer X.X.X.X)
ping 192.168.X.X

:: Ping Internet (DNS + ICMP)
ping google.com

:: Ping continu (Ctrl + C pour arrêter)
ping 8.8.8.8 -t
```

</details>

<details>
  <summary><strong>3) Routage et saut(s)</strong></summary>

```bat
:: Traceroute
tracert google.com

:: Route locale (table de routage)
route print
```

</details>

<details>
  <summary><strong>4) Résolution de noms</strong></summary>

```bat
:: Résolution simple (legacy)
nslookup google.com

:: Résolution + type d'enregistrement (ex. A, AAAA, NS)
nslookup -type=A google.com
```

</details>

<details>
  <summary><strong>5) Couches voisines & cache</strong></summary>

```bat
:: Cache ARP (voisins L2)
arp -a

:: Cache DNS client
ipconfig /displaydns

:: Vider le cache DNS
ipconfig /flushdns
```

</details>

<details>
  <summary><strong>6) Sessions et ports (informationnel)</strong></summary>

```bat
:: Connexions et ports à l'écoute
netstat -ano

:: Filtrer sur un port (ex. 443)
netstat -ano | find ":443"
```

</details>

<details>
  <summary><strong>7) Export & journalisation</strong></summary>

```bat
:: Export complet de la config réseau vers le Bureau
ipconfig /all > "%USERPROFILE%\Desktop\config_reseau.txt"

:: Traceroute vers un fichier
tracert google.com > "%USERPROFILE%\Desktop\tracert_google.txt"

:: Ping de 30 secondes approximatif (Ctrl+C pour arrêter puis copier le résumé)
ping google.com -t
```

</details>

<details>
  <summary><strong>8) Équivalents PowerShell utiles (optionnel)</strong></summary>

```powershell
# IP/DNS/Passerelle
Get-NetIPConfiguration

# Ping avancé (Test-NetConnection)
Test-NetConnection -ComputerName google.com
Test-NetConnection -ComputerName google.com -TraceRoute

# Résolution DNS moderne
Resolve-DnsName google.com

# Table de routage
Get-NetRoute | Sort-Object DestinationPrefix
```

</details>

<details>
  <summary><strong>9) Modèles “copier-coller rapide”</strong></summary>

```bat
:: Diagnostic minimal
ipconfig /all > "%USERPROFILE%\Desktop\config.txt"
ping 192.168.X.X
ping 8.8.8.8
ping google.com
tracert google.com > "%USERPROFILE%\Desktop\route_google.txt"
```

```powershell
# Résumé lisible
Get-NetIPConfiguration | Format-Table InterfaceAlias,IPv4Address,IPv4DefaultGateway,DnsServer -Auto
Test-NetConnection -ComputerName google.com -InformationLevel Detailed
```

</details>
