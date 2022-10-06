## TP Réseau 
## I. Exploration locale en solo
### 1. Affichage d'informations sur la pile TCP/IP locale

Interface wifi:
 Adresse IPv4: 10.33.16.69(préféré)
 Adresse physique: C0-E4-34-40-6F-99
 Description: Realtek 8822BE Wireless LAN 802.11ac PCI-E NIC
 
 Interface ethernet:
 pas de carte ethernet
 
 Gateway
 commande "ipconfig /all"
  l'adresse IP de la passerelle de votre carte WiFi : 10.33.19.254
  
  la MAC de la passerelle
  commande "arp -a" :   00-c0-e7-e0-04-4e 
  
 ##### Trouvez comment afficher les informations sur une carte IP (change selon l'OS)
 Procédé: 
 touche windows/panneau de configuration/réseau et internet/centre de réseau et de partage/wifi(wifi@ynov)/détails
 
 adresse ipv4: 10.33.16.69
 gateway: 10.33.19.254
 la mac: C0-E4-34-40-6F-99
 
 ### 2. Modifications des informations adresse ip
 
 procédé: 
 touche windows/panneau de configuration/réseau et internet/centre de réseau et de partage/wifi(wifi@ynov)/propriété/protocole internet version 4(TCP/IPv4)/utiliser l'adresse suivante(changement d'adresse)/ adresse ip: 10.33.16.4.
 
### A. Modification d'adresse IP (part 1)
🌞Utilisez l'interface graphique de votre OS pour changer d'adresse IP : 

Il est possible que vous perdiez l'accès internet. Que ce soit le cas ou non, expliquez pourquoi c'est possible de perdre son accès internet en faisant cette opération.

Quand on change l'adresse IP de notre ordinateur, celle-ci étant déjà utiliser, la connexion internet se coupe, alors il faut attendre qu'elle se déconnecte pour avoir son adresse ip. Tant que la machine est connectée au réseau, l'ordinateur n'a pas accés à internet. Pour re évoir accés à internet, il faut refaire le procéssus et remettre l'adresse ip de départ. 


## II. Exploration locale en duo

### 3.Modification de l'adresse IP
🌞 **Modifiez l'IP des deux machines pour qu'elles soient dans le même réseau**
#### procédé : 
"centre de réseau et partage" -> "ethernet" -> "propriétés" -> "Protocole Internet version 4 (TCP/IPv4)" -> "adresse IP : 10.10.10.88 et 10.10.10.222"


🌞 **Vérifier à l'aide d'une commande que votre IP a bien été changée**
#### commande ipconfig
"Carte Ethernet Ethernet :
 Adresse IPv4. . . . . . . . . . . . . .: 10.10.10.222"

🌞 **Vérifier que les deux machines se joignent**
#### commande PING 10.10.10.88
"Envoi d’une requête 'Ping'  10.10.10.88 avec 32 octets de données :
Réponse de 10.10.10.88 : octets=32 temps=1 ms TTL=128
Réponse de 10.10.10.88 : octets=32 temps=1 ms TTL=128
Réponse de 10.10.10.88 : octets=32 temps=1 ms TTL=128
Réponse de 10.10.10.88 : octets=32 temps=1 ms TTL=128

Statistiques Ping pour 10.10.10.88:
    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 1ms, Maximum = 1ms, Moyenne = 1ms"

🌞 **Déterminer l'adresse MAC de votre correspondant**
#### commande arp -a
 08-8f-c3-8b-df-d6

## 4.Utilisation d'un des deux comme gateway
🌞**Tester l'accès internet**
#### commande ping 8.8.8.8
Envoi d’une requête 'Ping'  8.8.8.8 avec 32 octets de données :
Réponse de 8.8.8.8 : octets=32 temps=22 ms TTL=113
Réponse de 8.8.8.8 : octets=32 temps=22 ms TTL=113
Réponse de 8.8.8.8 : octets=32 temps=21 ms TTL=113
Réponse de 8.8.8.8 : octets=32 temps=23 ms TTL=113

🌞 **Prouver que la connexion Internet passe bien par l'autre PC**
#### commande tracert -d 8.8.8.8

Détermination de l’itinéraire vers 8.8.8.8 avec un maximum de 30 sauts.

  1     1 ms     *        2 ms  192.168.137.1
  2     *        *        *     Délai d’attente de la demande dépassé.
  3     7 ms     6 ms     5 ms  10.33.19.254
  4     7 ms     5 ms     5 ms  77.196.149.137
  5    10 ms    11 ms     8 ms  212.30.97.108
  6    27 ms    21 ms    20 ms  77.136.172.222
  7    22 ms    22 ms    21 ms  77.136.172.221
  8    24 ms    23 ms    23 ms  194.6.144.186
  9    24 ms    23 ms    23 ms  194.6.144.186
 10    24 ms    22 ms    22 ms  72.14.194.30
 11    24 ms    23 ms    22 ms  172.253.69.49
 12    22 ms    23 ms    22 ms  108.170.238.107
 13    23 ms    22 ms    23 ms  8.8.8.8

### 5.Petit chat privé
 sur le PC serveur avec par exemple l'IP 192.168.1.1

C:\Users\Bayle\netcat-1.11> .\nc.exe -l -p 8888

 sur le PC client avec par exemple l'IP 192.168.1.2

"C:\Users\mathi\TP-réseau-03-10-2022\netcat-win32-1.11\netcat-1.11>.\nc.exe 192.168.1.1 8888
gh
coucou
ça fonctionne
wa c tro bien
uiiiiiiiiiiiii
coucou"

 Visualiser la connexion en cours

"netstat -a -n -b | Select-String 8888

  TCP    192.168.1.1:8888       0.0.0.0:0              LISTENING
TCP    192.168.1.1:8888       192.168.1.2:55861      ESTABLISHED
 [nc.exe]"

 Pour aller un peu plus loin

"netstat -a -n -b | Select-String 8888

  TCP    192.168.1.1:8888       0.0.0.0:0              LISTENING"

## 6. Firewall

 Activez et configurez votre firewall

touche windows -> pare-feu windows defender -> "regles de trafic entrant" -> "nouvelle regle" -> "suivant" -> "suivant" -> "autoriser la connection"; "suivant" -> cocher "domaine","privé" et "public"; "suivant" -> nom "ping" description "autoriser les pings de 192.168.1.1"; "terminer"

### ping

ping 192.168.1.1

Envoi d’une requête 'Ping'  192.168.1.1 avec 32 octets de données :
Réponse de 192.168.1.1 : octets=32 temps<1ms TTL=128
Réponse de 192.168.1.1 : octets=32 temps<1ms TTL=128
Réponse de 192.168.1.1 : octets=32 temps<1ms TTL=128
Réponse de 192.168.1.1 : octets=32 temps<1ms TTL=128

Statistiques Ping pour 192.168.1.1:
    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 0ms, Maximum = 0ms, Moyenne = 0ms
## III. Manipulations d'autres outils/protocoles côté client

### 1. DHCP
🌞Exploration du DHCP, depuis votre PC
 Serveur DHCP : 10.33.19.254

 La date d'expiration: vendredi 7 octobre 2022 8:50:33


### 2. DNS
🌞** Trouver l'adresse IP du serveur DNS que connaît votre ordinateur**
Serveurs DNS : 8.8.8.8

🌞 Utiliser, en ligne de commande l'outil nslookup (Windows, MacOS) ou dig (GNU/Linux, MacOS) pour faire des requêtes DNS à la main

google.com:

 Serveur :   dns.google
Address:  8.8.8.8

Réponse ne faisant pas autorité :
Nom :    google.com
Addresses:  2a00:1450:4007:80e::200e
          216.58.214.174

Ynov.com:

Serveur :   dns.google
Address:  8.8.8.8

Réponse ne faisant pas autorité :
Nom :    ynov.com
Addresses:  2606:4700:20::681a:ae9
          2606:4700:20::ac43:4ae2
          2606:4700:20::681a:be9
          104.26.11.233
          104.26.10.233
          172.67.74.226
          
Pour l'adresse 231.34.113.12:

          Address:  8.8.8.8
          
          Serveur :   dns.google
          
Pour l'adresse 78.34.2.17:

Address:  8.8.8.8

Serveur :   dns.google
Address:  8.8.8.8

Nom :    cable-78-34-2-17.nc.de
Address:  78.34.2.17

## IV. Wireshark
### 1. Intro Wireshark

![](https://i.imgur.com/vvMPlrV.png)






 
   
   
  
 
 
    