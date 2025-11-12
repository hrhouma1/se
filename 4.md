# Lab 1.2 — Observer le Trafic Réseau avec Wireshark (Windows)

## Sommaire

* [Objectif](#objectif)
* [Contexte professionnel](#contexte-professionnel)
* [Compétences visées](#compétences-visées)
* [Prérequis & consignes](#prérequis--consignes)
* [Durée estimée](#durée-estimée)
* [Rappels sécurité & limites](#rappels-sécurité--limites)
* [Partie 1 — Installation de Wireshark](#partie-1--installation-de-wireshark)
* [Partie 2 — Première capture](#partie-2--première-capture)
* [Partie 3 — Filtrage des paquets](#partie-3--filtrage-des-paquets)
* [Partie 4 — Capture et analyse DNS](#partie-4--capture-et-analyse-dns)
* [Partie 5 — Capture HTTP (trafic web)](#partie-5--capture-http-trafic-web)
* [Partie 6 — Analyse d’une communication complète](#partie-6--analyse-dune-communication-complète)
* [Partie 7 — Exercices pratiques](#partie-7--exercices-pratiques)
* [Partie 8 — Statistiques réseau](#partie-8--statistiques-réseau)
* [Livrables](#livrables)
* [Critères d’évaluation](#critères-dévaluation)
* [Filtres Wireshark de référence](#filtres-wireshark-de-référence)
* [Raccourcis clavier utiles](#raccourcis-clavier-utiles)
* [Dépannage](#dépannage)
* [Aller plus loin (optionnel)](#aller-plus-loin-optionnel)
* [Ressources additionnelles](#ressources-additionnelles)
* [Prochaines étapes](#prochaines-étapes)
* [Fiche mémo](#fiche-mémo)
* [Annexe — Toutes les commandes & filtres](#annexe--toutes-les-commandes--filtres)

---

<details>
  <summary><strong>Objectif</strong></summary>

Installer, configurer et utiliser Wireshark pour capturer du trafic en temps réel, filtrer et analyser des paquets clés (ICMP, DNS, HTTP, TCP/UDP) et interpréter une communication réseau de bout en bout.

</details>

<details>
  <summary><strong>Contexte professionnel</strong></summary>

Wireshark est l’outil d’analyse réseau de référence. On s’en sert pour diagnostiquer des incidents, mesurer des performances, comprendre le comportement applicatif, détecter des anomalies sécurité et valider des configurations.

</details>

<details>
  <summary><strong>Compétences visées</strong></summary>

* Capturer du trafic sur l’interface correcte avec Npcap.
* Utiliser les **filtres d’affichage** et **filtres de capture**.
* Lire les en-têtes de protocoles (ICMP, DNS, HTTP, TCP/UDP).
* Reconstruire une session (Follow TCP Stream) et reconnaître le **three-way handshake**.

</details>

<details>
  <summary><strong>Prérequis & consignes</strong></summary>

<strong>Prérequis</strong>

* Lab 1.1 complété.
* Droits **administrateur** sur la machine.
* Connexion Internet active.

<strong>Consignes</strong>

1. Installer Wireshark + **Npcap** (mode compatibilité WinPcap).
2. Capturer uniquement votre propre trafic et celui des environnements autorisés.
3. Sauvegarder les captures au format **.pcapng** et fournir des captures d’écran annotées.

</details>

<details>
  <summary><strong>Durée estimée</strong></summary>

45 minutes.

</details>

<details>
  <summary><strong>Rappels sécurité & limites</strong></summary>

* Le trafic peut contenir des données sensibles. Respecter la confidentialité et la légalité.
* Beaucoup de sites utilisent **HTTPS/TLS** : le contenu applicatif ne sera pas lisible sans déchiffrement légitime.
* Sur certains postes/FAI, l’ICMP ou certaines interfaces peuvent être filtrées.

</details>

---

## Partie 1 — Installation de Wireshark

<details>
  <summary><strong>Étape 1.1 — Téléchargement</strong></summary>

* Aller sur `https://www.wireshark.org/download.html`
* Télécharger **Windows Installer (64-bit)**.
* Lancer l’installeur.

</details>

<details>
  <summary><strong>Étape 1.2 — Installation (recommandée)</strong></summary>

* Conserver les **options par défaut**.
* **Installer Npcap** quand proposé.
* Cocher « Install Npcap in WinPcap API-compatible mode » si demandé.

</details>

<details>
  <summary><strong>Étape 1.3 — Premier lancement</strong></summary>

* Ouvrir Wireshark (administrateur si demandé).
* Identifier l’**interface active** (Ethernet/Wi-Fi) — celle qui montre une activité (petit graphe).

**Questions**

1. Combien d’interfaces réseau sont listées ?
2. Laquelle montre de l’activité ?

</details>

---

## Partie 2 — Première capture

<details>
  <summary><strong>Étape 2.1 — Démarrer la capture</strong></summary>

* Double-cliquer l’interface active.
* Observer les paquets qui défilent (Time, Source, Destination, Protocol, Info).
* Les couleurs indiquent des familles de protocoles.

</details>

<details>
  <summary><strong>Étape 2.2 — Générer du trafic simple (ICMP)</strong></summary>

Dans **Invite de commandes (cmd)** :

```bat
ping google.com -n 4
```

Dans Wireshark : repérer **Echo (ping) request** et **Echo (ping) reply**.

</details>

<details>
  <summary><strong>Étape 2.3 — Arrêter la capture</strong></summary>

* Cliquer le bouton **Stop** (carré rouge).

**Questions**
3) Combien de paquets au total ?
4) Identifiez-vous les 4 pings envoyés ?

</details>

---

## Partie 3 — Filtrage des paquets

<details>
  <summary><strong>Étape 3.1 — Filtre ICMP (ping)</strong></summary>

Dans la barre de filtres (haut) :

```
icmp
```

Entrée → n’affiche que les paquets ICMP.
Observer requêtes et réponses.

</details>

<details>
  <summary><strong>Étape 3.2 — Filtre par adresse IP</strong></summary>

```
ip.addr == 8.8.8.8
```

(Remplacer par l’IP observée pour `google.com`).

</details>

<details>
  <summary><strong>Étape 3.3 — Effacer un filtre</strong></summary>

Cliquer sur le **X** à droite de la barre de filtre pour revenir à l’affichage complet.

</details>

---

## Partie 4 — Capture et analyse DNS

<details>
  <summary><strong>Étape 4.1 — Nouvelle capture dédiée DNS</strong></summary>

* Fichier → Fermer la capture précédente.
* Démarrer une **nouvelle capture**.
* Appliquer immédiatement le filtre d’affichage :

```
dns
```

</details>

<details>
  <summary><strong>Étape 4.2 — Générer des requêtes DNS</strong></summary>

Dans **cmd** :

```bat
nslookup facebook.com
nslookup youtube.com
nslookup wikipedia.org
```

</details>

<details>
  <summary><strong>Étape 4.3 — Analyser</strong></summary>

* Sélectionner une **Standard query** → panneau du bas :

  * Domain Name System (query) → Queries → **Name**.
* Sélectionner une **Standard query response** →

  * Domain Name System (response) → Answers → **IP retournée**.

**Questions**
5) Quelle est l’adresse IP de `facebook.com` ?
6) Quel serveur DNS est interrogé (colonne Destination) ?
7) Combien de temps a pris la requête DNS (colonne Time ou champs Transaction Time si présent) ?

</details>

---

## Partie 5 — Capture HTTP (trafic web)

<details>
  <summary><strong>Étape 5.1 — Préparer la capture HTTP</strong></summary>

* Nouvelle capture.
* Filtre d’affichage :

```
http
```

(Remarque : **HTTP non chiffré** uniquement.)

</details>

<details>
  <summary><strong>Étape 5.2 — Générer du trafic HTTP</strong></summary>

Visiter dans le navigateur :

* `http://neverssl.com`
* `http://example.com`

</details>

<details>
  <summary><strong>Étape 5.3 — Analyser une requête HTTP</strong></summary>

* Sélectionner une ligne **GET**.
* Dérouler **Hypertext Transfer Protocol** pour voir : **headers**, **Host**, **User-Agent**.

**Questions**
8) Quel est le **User-Agent** du navigateur ?
9) Quelles informations sont **visibles en clair** dans la requête HTTP ?
10) Pourquoi **HTTPS** est-il plus sécurisé ? (Refaire l’essai avec `https://google.com` et comparer.)

</details>

---

## Partie 6 — Analyse d’une communication complète

<details>
  <summary><strong>Étape 6.1 — Three-Way Handshake TCP</strong></summary>

* Nouvelle capture, filtre d’affichage :

```
tcp
```

* Visiter `http://example.com`, arrêter.
* Rechercher la séquence : **[SYN] → [SYN, ACK] → [ACK]** vers le port **80**.

**Questions**
11) Trouvez-vous le three-way handshake ?
12) Quels sont les numéros **Seq** et **Ack** des 3 premiers paquets ?

</details>

<details>
  <summary><strong>Étape 6.2 — Follow TCP Stream</strong></summary>

* Clic droit sur un paquet HTTP → **Follow → TCP Stream**.
* Observer la conversation :

  * **Rouge** : client → serveur.
  * **Bleu** : serveur → client.
  * Le **HTML** est lisible en HTTP.

</details>

---

## Partie 7 — Exercices pratiques

<details>
  <summary><strong>Exercice 7.1 — HTTP vs HTTPS</strong></summary>

* Capturer `http://neverssl.com` puis `https://google.com`.
* Tenter **Follow TCP Stream** sur les deux.

**Question**
13) Que voyez-vous en **HTTP** que vous ne voyez pas en **HTTPS** ?

</details>

<details>
  <summary><strong>Exercice 7.2 — Identification de protocoles</strong></summary>

Tester :

* `https://youtube.com` (chercher **QUIC**/**HTTP/2**).
* Lancer une **mise à jour Windows** (protocoles Microsoft).
* Ouvrir **Discord** ou **Teams** (**WebRTC**).

**Questions**
14) Quels **nouveaux protocoles** avez-vous découverts ?
15) Combien de **connexions TCP** s’ouvrent quand vous visitez `youtube.com` ?

</details>

<details>
  <summary><strong>Exercice 7.3 — Diagnostic</strong></summary>

* Démarrer une capture.
* Dans **cmd** :

```bat
ping 1.2.3.4 -n 2
```

**Questions**
16) Voyez-vous des **ICMP Destination unreachable** ?
17) **Qui** envoie ces messages (colonne Source) ?

</details>

---

## Partie 8 — Statistiques réseau

<details>
  <summary><strong>Étape 8.1 — Hiérarchie des protocoles</strong></summary>

Menu **Statistics → Protocol Hierarchy** : répartition par protocole.

**Questions**
18) Quel protocole domine votre capture ?
19) Quel pourcentage est du trafic **TLS** (chiffré) ?

</details>

<details>
  <summary><strong>Étape 8.2 — Conversations</strong></summary>

Menu **Statistics → Conversations** (onglet IPv4).

**Questions**
20) Avec quelle IP avez-vous échangé le plus de données ?
21) Combien de **conversations** distinctes ?

</details>

<details>
  <summary><strong>Étape 8.3 — Endpoints</strong></summary>

Menu **Statistics → Endpoints** : liste des hôtes ayant communiqué.

</details>

---

## Livrables

* **Fichier .pcapng** contenant : ICMP, DNS, HTTP.
* **Document de réponses** (Word/PDF) avec les **21 questions**.
* **3 captures d’écran** :

  1. Affichage filtré **ICMP**
  2. Requête **DNS** (détails)
  3. **Follow TCP Stream** d’une requête HTTP
* **Analyse comparative HTTP vs HTTPS** (1 page) : données visibles / cachées et impact sécurité.

---

## Critères d’évaluation

| Critère                                         | Points |
| ----------------------------------------------- | :----: |
| Capture contenant ICMP, DNS, HTTP               |   25%  |
| Réponses correctes et argumentées aux questions |   35%  |
| Captures d’écran pertinentes et annotées        |   20%  |
| Analyse comparative HTTP/HTTPS                  |   15%  |
| Qualité de la documentation (clarté, structure) |   5%   |

---

## Filtres Wireshark de référence

| Filtre                           | Description                     | Exemple                          |
| -------------------------------- | ------------------------------- | -------------------------------- |
| `icmp`                           | Paquets ping                    | `icmp`                           |
| `dns`                            | Requêtes/réponses DNS           | `dns`                            |
| `http`                           | Trafic HTTP non chiffré         | `http`                           |
| `tcp`                            | Tous les paquets TCP            | `tcp`                            |
| `udp`                            | Tous les paquets UDP            | `udp`                            |
| `ip.addr == X`                   | Trafic vers/depuis IP X         | `ip.addr == 8.8.8.8`             |
| `ip.src == X`                    | Source X uniquement             | `ip.src == 192.168.1.1`          |
| `ip.dst == X`                    | Destination X uniquement        | `ip.dst == 8.8.8.8`              |
| `tcp.port == X`                  | Trafic TCP sur port X           | `tcp.port == 80`                 |
| `http.request.method == "GET"`   | Requêtes HTTP GET               | `http.request.method == "GET"`   |
| `dns.qry.name contains "google"` | Requêtes DNS contenant “google” | `dns.qry.name contains "google"` |

---

## Raccourcis clavier utiles

| Raccourci    | Action                      |
| ------------ | --------------------------- |
| **Ctrl + E** | Démarrer/Arrêter la capture |
| **Ctrl + K** | Options de capture          |
| **Ctrl + W** | Fermer la capture           |
| **Ctrl + F** | Rechercher un paquet        |
| **Ctrl + G** | Aller au paquet N           |
| **Ctrl + R** | Recharger le fichier        |
| **Ctrl + /** | Barre de filtre (focus)     |

---

## Dépannage

**Problème :** « Couldn't run dumpcap »
**Cause probable :** permissions ou Npcap.
**Solution :** lancer Wireshark **en admin**, réinstaller **Npcap** (mode WinPcap compatible).

**Problème :** Aucune interface visible
**Solution :** réinstaller Npcap ; vérifier que l’adaptateur n’est pas **désactivé** (Panneau de configuration → Réseau).

**Problème :** « The capture session could not be initiated »
**Solution :** une autre appli occupe l’interface (VPN, Hyper-V, WSL) — fermer, puis réessayer ; redémarrer si besoin.

**Problème :** Trop de paquets, lecture difficile
**Solution :** utiliser des **filtres d’affichage**, fermer les apps bavardes (cloud sync), **capturer** uniquement pendant l’action visée.

> Remarque : le message `Couldn't run /usr/bin/dumpcap` est typique Linux/macOS. Sous Windows, l’exécutable est `dumpcap.exe` fourni avec Wireshark.

---

## Aller plus loin (optionnel)

* Identifier une application (Discord/Spotify/Steam) : serveurs contactés, protocoles, volumes.
* Mesurer des **temps** : résolution DNS, handshake TCP, temps de premier octet.
* Découvrir 3 protocoles que vous ne connaissiez pas et résumer leur usage.

---

## Ressources additionnelles

* **Wireshark User Guide**
* **Wireshark Display Filters Reference**
* **Wireshark Sample Captures**
* Introduction aux protocoles réseau (TCP/UDP/DNS/HTTP/QUIC)

---

## Prochaines étapes

Lab suivant : inventaire approfondi du système (processus, services), et cartographie de la configuration de sécurité.

---

## Fiche mémo

* **Démarrer/stopper** : Ctrl+E
* **Interface active** : choisir celle avec du trafic
* **Filtres fréquents** : `icmp`, `dns`, `http`, `tcp.port == 80`, `ip.addr == <IP>`
* **DNS rapide** : `dns.qry.name contains "exemple"`
* **Reconstruction** : clic droit → **Follow TCP Stream**
* **Statistiques** : **Statistics → Protocol Hierarchy / Conversations / Endpoints**
* **Sauvegarder** : `.pcapng` + captures d’écran annotées

---

## Annexe — Toutes les commandes & filtres

<details>
  <summary><strong>1) Génération de trafic (cmd)</strong></summary>

```bat
:: ICMP (4 paquets)
ping google.com -n 4

:: DNS - résolution simple
nslookup facebook.com
nslookup youtube.com
nslookup wikipedia.org
```

</details>

<details>
  <summary><strong>2) Filtres d’affichage (Wireshark)</strong></summary>

```text
icmp
dns
http
tcp
udp
ip.addr == 8.8.8.8
ip.src == 192.168.1.1
ip.dst == 8.8.8.8
tcp.port == 80
http.request.method == "GET"
dns.qry.name contains "google"
tls      ; trafic chiffré TLS (en-têtes visibles, contenu chiffré)
quic     ; QUIC (UDP) si supporté par le site (ex. YouTube)
```

</details>

<details>
  <summary><strong>3) Filtres de capture (optionnel, plus stricts)</strong></summary>

> À définir avant de démarrer la capture (Capture → Options → Capture Filter).

```text
icmp
port 53
tcp port 80
host 8.8.8.8
net 192.168.0.0/16
```

</details>

<details>
  <summary><strong>4) Parcours d’analyse</strong></summary>

```text
1) Démarrer capture sur l’interface active
2) Générer trafic (ping / nslookup / http)
3) Appliquer filtres (icmp / dns / http)
4) Inspecter un paquet (panneau du bas : en-têtes / champs)
5) Follow TCP Stream pour conversation HTTP
6) Statistiques → Protocol Hierarchy / Conversations / Endpoints
7) Sauvegarder .pcapng et captures d’écran
```

</details>

<details>
  <summary><strong>5) Modèle de mini-rapport</strong></summary>

```text
Titre, Nom, Date
Environnement : OS, interface, IP locale
ICMP : latences, pertes
DNS : noms résolus, serveurs interrogés
HTTP : requêtes GET, User-Agent, hôtes
Handshake TCP : présence, ports, Seq/Ack
Statistiques : protocole principal, top IP en volume
Conclusion : résumé et points notables
```

</details>
