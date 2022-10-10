# TP 2 réseau

# TP2 : Ethernet, IP, et ARP

Dans ce TP on va approfondir trois protocoles, qu'on a survolé jusqu'alors :

-   **IPv4** _(Internet Protocol Version 4)_ : gestion des adresses IP
    -   on va aussi parler d'ICMP, de DHCP, bref de tous les potes d'IP quoi !
-   **Ethernet** : gestion des adresses MAC
-   **ARP** _(Address Resolution Protocol)_ : permet de trouver l'adresse MAC de quelqu'un sur notre réseau dont on connaît l'adresse IP

# [](#sommaire)Sommaire

-   [TP2 : Ethernet, IP, et ARP](#tp2--ethernet-ip-et-arp)
-   [Sommaire](#sommaire)
-   [0. Prérequis](#0-pr%C3%A9requis)
-   [I. Setup IP](#i-setup-ip)
-   [II. ARP my bro](#ii-arp-my-bro)
-   [II.5 Interlude hackerzz](#ii5-interlude-hackerzz)
-   [III. DHCP you too my brooo](#iii-dhcp-you-too-my-brooo)

# [](#0-pr%C3%A9requis)0. Prérequis

**Il vous faudra deux machines**, vous êtes libres :

-   toujours possible de se connecter à deux avec un câble
-   sinon, votre PC + une VM ça fait le taf, c'est pareil
    -   je peux aider sur le setup, comme d'hab

> Je conseille à tous les gens qui n'ont pas de port RJ45 de go PC + VM pour faire vous-mêmes les manips, mais on fait au plus simple hein.

---

**Toutes les manipulations devront être effectuées depuis la ligne de commande.** Donc normalement, plus de screens.

**Pour Wireshark, c'est pareil,** NO SCREENS. La marche à suivre :

-   vous capturez le trafic que vous avez à capturer
-   vous stoppez la capture (bouton carré rouge en haut à gauche)
-   vous sélectionnez les paquets/trames intéressants (CTRL + clic)
-   File > Export Specified Packets...
-   dans le menu qui s'ouvre, cochez en bas "Selected packets only"
-   sauvegardez, ça produit un fichier `.pcapng` (qu'on appelle communément "un ptit PCAP frer") que vous livrerez dans le dépôt git

**Si vous voyez le p'tit pote 🦈 c'est qu'il y a un PCAP à produire et à mettre dans votre dépôt git de rendu.**

# [](#i-setup-ip)I. Setup IP

Le lab, il vous faut deux machines :

-   les deux machines doivent être connectées physiquement
-   vous devez choisir vous-mêmes les IPs à attribuer sur les interfaces réseau, les contraintes :
    -   IPs privées (évidemment n_n)
    -   dans un réseau qui peut contenir au moins 1000 adresses IP (il faut donc choisir un masque adapté)
    -   oui c'est random, on s'exerce c'est tout, p'tit jog en se levant c:
    -   le masque choisi doit être le plus grand possible (le plus proche de 32 possible) afin que le réseau soit le plus petit possible

🌞 **Mettez en place une configuration réseau fonctionnelle entre les deux machines**

```
 Adresse IPv4. . . . . . . . . . . . . .: 172.16.32.1
 Adresse IPv4. . . . . . . . . . . . . .: 172.16.32.2
 Adresse Broadcast        = 172.16.35.255
 (commande utiliser : ipconfig )
```


🌞 **Prouvez que la connexion est fonctionnelle entre les deux machines**

```
ping 172.16.32.2

Envoi d’une requête 'Ping'  172.16.32.2 avec 32 octets de données :
Réponse de 172.16.32.2 : octets=32 temps=1 ms TTL=128
Réponse de 172.16.32.2 : octets=32 temps=1 ms TTL=128
Réponse de 172.16.32.2 : octets=32 temps=1 ms TTL=128
Réponse de 172.16.32.2 : octets=32 temps=1 ms TTL=128

Statistiques Ping pour 172.16.32.2:
    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 1ms, Maximum = 1ms, Moyenne = 1ms
```

🌞 **Wireshark it**

-   **déterminez, grâce à Wireshark, quel type de paquet ICMP est envoyé par `ping`**
```
Quand il y a écrit request c'est celui qu'on envoie.
Et celui où il y a écrit reply c'est celui reçu en retour.
```

🦈 **PCAP qui contient les paquets ICMP qui vous ont permis d'identifier les types ICMP**

[ma capture pcap ICMP](./ping_icmp.pcapng)

# [](#ii-arp-my-bro)II. ARP my bro

ARP permet, pour rappel, de résoudre la situation suivante :

-   pour communiquer avec quelqu'un dans un LAN, il **FAUT** connaître son adresse MAC
-   on admet un PC1 et un PC2 dans le même LAN :
    -   PC1 veut joindre PC2
    -   PC1 et PC2 ont une IP correctement définie
    -   PC1 a besoin de connaître la MAC de PC2 pour lui envoyer des messages
    -   **dans cette situation, PC1 va utilise le protocole ARP pour connaître la MAC de PC2**
    -   une fois que PC1 connaît la mac de PC2, il l'enregistre dans sa **table ARP**

🌞 **Check the ARP table**

```
arp -a

Interface : 172.16.32.1 --- 0xe
  Adresse Internet      Adresse physique      Type
  172.16.32.2           34-73-5a-ea-15-f2     dynamique

ipconfig

Passerelle par défaut. . . . . . . . . : 10.33.19.254
```

🌞 **Manipuler la table ARP**

```
arp -d
arp -a 

Interface : 172.16.32.1 --- 0xe
  Adresse Internet      Adresse physique      Type
  224.0.0.22            01-00-5e-00-00-16     statique

ping 172.16.32.2

Envoi d’une requête 'Ping'  172.16.32.2 avec 32 octets de données :
Réponse de 172.16.32.2 : octets=32 temps=1 ms TTL=128
Réponse de 172.16.32.2 : octets=32 temps=1 ms TTL=128
Réponse de 172.16.32.2 : octets=32 temps<1ms TTL=128
Réponse de 172.16.32.2 : octets=32 temps=1 ms TTL=128

Statistiques Ping pour 172.16.32.2:
    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 0ms, Maximum = 1ms, Moyenne = 0ms

arp -a

Interface : 172.16.32.1 --- 0xe
  Adresse Internet      Adresse physique      Type
  172.16.32.2           34-73-5a-ea-15-f2     dynamique
  172.16.35.255         ff-ff-ff-ff-ff-ff     statique
  224.0.0.22            01-00-5e-00-00-16     statique
  224.0.0.251           01-00-5e-00-00-fb     statique
  224.0.0.252           01-00-5e-00-00-fc     statique
  239.255.255.250       01-00-5e-7f-ff-fa     statique
```

🌞 **Wireshark it**

```
src : (08:8f:c3:8b:df:d6) (moi qui request vers le broadcast je ne sais pas pourquoi d'ailleurs) dst : (broadcast ff:ff:ff:ff:ff:ff)
```

🦈 **PCAP qui contient les trames ARP**

[ma capture pcap ARP](./ARP_Reply_request.pcapng)

> L'échange ARP est constitué de deux trames : un ARP broadcast et un ARP reply.

# [](#ii5-interlude-hackerzz)II.5 Interlude hackerzz

**Chose promise chose due, on va voir les bases de l'usurpation d'identité en réseau : on va parler d'_ARP poisoning_.**

> On peut aussi trouver _ARP cache poisoning_ ou encore _ARP spoofing_, ça désigne la même chose.

Le principe est simple : on va "empoisonner" la table ARP de quelqu'un d'autre.  
Plus concrètement, on va essayer d'introduire des fausses informations dans la table ARP de quelqu'un d'autre.

Entre introduire des fausses infos et usurper l'identité de quelqu'un il n'y a qu'un pas hihi.

---

➜ **Le principe de l'attaque**

-   on admet Alice, Bob et Eve, tous dans un LAN, chacun leur PC
-   leur configuration IP est ok, tout va bien dans le meilleur des mondes
-   **Eve 'lé pa jonti** _(ou juste un agent de la CIA)_ : elle aimerait s'immiscer dans les conversations de Alice et Bob
    -   pour ce faire, Eve va empoisonner la table ARP de Bob, pour se faire passer pour Alice
    -   elle va aussi empoisonner la table ARP d'Alice, pour se faire passer pour Bob
    -   ainsi, tous les messages que s'envoient Alice et Bob seront en réalité envoyés à Eve

➜ **La place de ARP dans tout ça**

-   ARP est un principe de question -> réponse (broadcast -> _reply_)
-   IL SE TROUVE qu'on peut envoyer des _reply_ à quelqu'un qui n'a rien demandé :)
-   il faut donc simplement envoyer :
    -   une trame ARP reply à Alice qui dit "l'IP de Bob se trouve à la MAC de Eve" (IP B -> MAC E)
    -   une trame ARP reply à Bob qui dit "l'IP de Alice se trouve à la MAC de Eve" (IP A -> MAC E)
-   ha ouais, et pour être sûr que ça reste en place, il faut SPAM sa mum, genre 1 reply chacun toutes les secondes ou truc du genre
    -   bah ui ! Sinon on risque que la table ARP d'Alice ou Bob se vide naturellement, et que l'échange ARP normal survienne
    -   aussi, c'est un truc possible, mais pas normal dans cette utilisation là, donc des fois bon, ça chie, DONC ON SPAM

---

➜ J'peux vous aider à le mettre en place, mais **vous le faites uniquement dans un cadre privé, chez vous, ou avec des VMs**

➜ **Je vous conseille 3 machines Linux**, Alice Bob et Eve. La commande `[arping](https://sandilands.info/sgordon/arp-spoofing-on-wired-lan)` pourra vous carry : elle permet d'envoyer manuellement des trames ARP avec le contenu de votre choix.

GLHF.

# [](#iii-dhcp-you-too-my-brooo)III. DHCP you too my brooo

_DHCP_ pour _Dynamic Host Configuration Protocol_ est notre p'tit pote qui nous file des IPs quand on arrive dans un réseau, parce que c'est chiant de le faire à la main :)

Quand on arrive dans un réseau, notre PC contacte un serveur DHCP, et récupère généralement 3 infos :

-   **1.** une IP à utiliser
-   **2.** l'adresse IP de la passerelle du réseau
-   **3.** l'adresse d'un serveur DNS joignable depuis ce réseau

L'échange DHCP entre un client et le serveur DHCP consiste en 4 trames : **DORA**, que je vous laisse chercher sur le web vous-mêmes : D

🌞 **Wireshark it**

```
Discover
Offer
Request
ACK
```

🦈 **PCAP qui contient l'échange DORA**

[Ma capture PCAP DORA](./DORA_DHCP.pcapng)
