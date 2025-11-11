

### 1. Idée générale

* Dans un ordinateur, **toutes les pièces doivent se parler** : processeur, mémoire, disque, carte graphique, clavier, souris…
* Pour ça, il faut :

  * des **fils** pour transporter les infos → on appelle ça un **bus** ;
  * des **prises** pour brancher les choses → ce sont les **connecteurs** ;
  * des **règles de discussion** → ce sont les **interfaces et protocoles**.

---

### 2. Le bus (pour eux : “les fils sur la carte mère”)

* Quand je dis **bus**, vous pouvez traduire par :
  **« les fils qui relient le processeur, la mémoire et les autres composants »**.
* Ce bus transporte :

  * les **données** (les 0 et 1),
  * les **adresses** (où lire/écrire),
  * des **ordres** (lire, écrire, interrompre, etc.).
* On ne voit pas ces fils comme des câbles :
  ils sont **imprimés sur la carte mère**, comme des petites routes en cuivre.

---

### 3. Le connecteur (pour eux : “la prise”)

* Quand je dis **connecteur**, pensez simplement à une **prise** :

  * la **prise USB** où vous branchez votre clé,
  * la prise où on branche la **carte graphique**,
  * la prise où on branche le **câble réseau**, etc.
* Un connecteur, c’est :

  * **une forme précise**, avec un certain nombre de petites broches métalliques ;
  * ce qui permet de **fixer** une carte ou un câble et d’avoir un **contact électrique** fiable.
* Donc :

  * **Bus** = les fils à l’intérieur.
  * **Connecteur** = là où vous **branchez** quelque chose.

---

### 4. Interface et protocole (pour eux : “la façon de parler”)

* Imaginez deux personnes qui se parlent :

  * elles doivent parler la **même langue**,
  * suivre un minimum de **règles** (ne pas parler en même temps, répondre aux questions, etc.).
* Dans un ordinateur :

  * l’**interface**, c’est le **type de conversation** (par exemple : “on envoie des blocs de 512 octets, à telle vitesse, dans tel ordre”) ;
  * le **protocole**, ce sont les **règles détaillées** :
    qui commence à parler, comment on dit “j’ai fini”, comment on signale une erreur, etc.
* Retenez surtout :

  * **Interface/protocole = les règles de discussion entre deux appareils**.
  * Sans ces règles, même s’ils sont bien branchés, ils ne se comprennent pas.

---

### 5. Bus partagé vs point-à-point (sans jargon)

* **Bus partagé** :

  * Imaginez une **seule ligne téléphonique** avec plusieurs personnes dessus.
  * Si deux personnes parlent en même temps → on ne comprend plus rien.
  * Il faut attendre son tour → tout le monde se **partage la même “route”**.
* **Bus point-à-point** :

  * Ici, chaque personne a **sa propre ligne**.
  * Personne ne gêne personne, chaque conversation est **indépendante**.
* Idée à retenir :

  * **Partagé** = plusieurs appareils sur le même chemin → ça peut bouchonner.
  * **Point-à-point** = un chemin par appareil → plus simple, plus rapide, pas de collision.

---

### 6. Rôle de la carte mère (version super simple)

* La **carte mère**, c’est :

  * la **grande planche verte** sur laquelle on pose tout le reste ;
  * elle contient les **fils (bus)** qui relient les composants ;
  * elle fournit les **prises (connecteurs)** pour brancher :

    * la RAM,
    * la carte graphique,
    * les disques,
    * les câbles USB, réseau, etc.
* Elle sert aussi à :

  * **distribuer l’électricité** à tous les composants ;
  * **organiser la circulation** des données : qui parle à qui, par quel chemin.

---

### 7. Résumé ultra-court à leur répéter

* **Bus** = les fils qui relient les composants.
* **Connecteur** = la prise où on branche un câble ou une carte.
* **Interface / protocole** = les règles pour se parler correctement.
* **Carte mère** = la grande “route centrale” qui relie tout et donne des prises à tout le monde.

