# TP 3  : On va router des trucs
## I.ARP
### 1.Echange ARP
🌞Générer des requêtes ARP
commande: ssh oceane@10.3.1.12 adresse IP marcel que j'ai ping dans john. 
```
ping 10.3.1.12
PING 10.3.1.12 (10.3.1.12) 56(84) bytes of data.
64 bytes from 10.3.1.12: icmp_seq=1 ttl=64 time=0.993 ms
64 bytes from 10.3.1.12: icmp_seq=2 ttl=64 time=1.60 ms
64 bytes from 10.3.1.12: icmp_seq=3 ttl=64 time=1.55 ms
64 bytes from 10.3.1.12: icmp_seq=4 ttl=64 time=1.20 ms
64 bytes from 10.3.1.12: icmp_seq=5 ttl=64 time=0.927 ms
64 bytes from 10.3.1.12: icmp_seq=6 ttl=64 time=1.50 ms
64 bytes from 10.3.1.12: icmp_seq=7 ttl=64 time=0.908 ms
64 bytes from 10.3.1.12: icmp_seq=8 ttl=64 time=1.44 ms
64 bytes from 10.3.1.12: icmp_seq=9 ttl=64 time=1.59 ms
64 bytes from 10.3.1.12: icmp_seq=10 ttl=64 time=1.44 ms
64 bytes from 10.3.1.12: icmp_seq=11 ttl=64 time=1.45 ms
64 bytes from 10.3.1.12: icmp_seq=12 ttl=64 time=1.46 ms
64 bytes from 10.3.1.12: icmp_seq=13 ttl=64 time=1.61 ms
64 bytes from 10.3.1.12: icmp_seq=14 ttl=64 time=0.880 ms
64 bytes from 10.3.1.12: icmp_seq=15 ttl=64 time=0.830 ms
64 bytes from 10.3.1.12: icmp_seq=16 ttl=64 time=0.602 ms
64 bytes from 10.3.1.12: icmp_seq=17 ttl=64 time=0.904 ms
64 bytes from 10.3.1.12: icmp_seq=18 ttl=64 time=0.628 ms
64 bytes from 10.3.1.12: icmp_seq=19 ttl=64 time=0.819 ms
64 bytes from 10.3.1.12: icmp_seq=20 ttl=64 time=0.601 ms
64 bytes from 10.3.1.12: icmp_seq=21 ttl=64 time=1.35 ms
^C
--- 10.3.1.12 ping statistics ---
21 packets transmitted, 21 received, 0% packet loss, time 20164ms
rtt min/avg/max/mdev = 0.601/1.156/1.611/0.354 ms
```
- pc john : 
[oceane@localhost ~]$ ip neigh show
10.3.1.12 dev enp0s8 lladdr 08:00:27:12:81:c3 STALE
10.3.1.1 dev enp0s8 lladdr 0a:00:27:00:00:2a REACHABLE
- pc marcel:
[oceane@localhost ~]$ ip neigh show
10.3.1.1 dev enp0s8 lladdr 0a:00:27:00:00:2a DELAY
10.3.1.11 dev enp0s8 lladdr 08:00:27:d3:88:33 STALE


pour voir la mac de Marcel dans le pc de john: ip neigh show
pour voir la mac de Marcel depuis le pc de Marcel: ip a 

2. Analyse de trames
🌞Analyse de trames

[Analyse de trames](./toto.pcap)

## II. Routage
🌞Activer le routage sur le noeud router


[oceane@localhost ~]$ sudo firewall-cmd --add-masquerade --zone=public
[sudo] password for oceane:
success
[oceane@localhost ~]$
[oceane@localhost ~]$
[oceane@localhost ~]$
[oceane@localhost ~]$ sudo firewall-cmd --add-masquerade --zone=public --permanent
success
[oceane@localhost ~]$ sudo firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s8 enp0s9
  sources:
  services: cockpit dhcpv6-client ssh
  ports:
  protocols:
  forward: yes
  masquerade: yes
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:
  
  🌞Ajouter les routes statiques nécessaires pour que john et marcel puissent se ping
  
 Commande pour ajouter les routes statiques:
 sudo ip route add 10.3.2.0/24 via 10.3.1.254 dev enp0s8
 
 sudo ip route add 10.3.1.0/24 via 10.3.2.254 dev  enp0s8

164 bytes from 10.3.2.12: icmp_seq=1 ttl=63 time=1.52 ms
64 bytes from 10.3.2.12: icmp_seq=2 ttl=63 time=1.07 ms
64 bytes from 10.3.2.12: icmp_seq=3 ttl=63 time=1.02 ms
64 bytes from 10.3.2.12: icmp_seq=4 ttl=63 time=0.862 ms
^C
--- 10.3.2.12 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3005ms
rtt min/avg/max/mdev = 0.862/1.117/1.521/0.245 ms


2. Analyse de trames
🌞Analyse des échanges ARP

| ordre | type trame  | IP source | MAC source              | IP destination | MAC destination            |
|-------|-------------|-----------|-------------------------|----------------|----------------------------|
| 1     | Requête ARP | x         |`marcel` `AA:BB:CC:DD:EE`| x              | Broadcast `FF:FF:FF:FF:FF` |
| 2     | Réponse ARP | x         |router 08:00:27:2f:71:d6 | x              | `marcel` `AA:BB:CC:DD:EE`  |
| ...   | ...         | ...       | ...                     |                |                            |
| ?     | Ping        |john 10.3.1.11 | john 08:00:27:a4:21:98 | router 10.3.1.254 | router 08:00:27:2f:71:d6  |
| ?     | Pong        |router 10.3.1.254 | router 08:00:27:2f:71:d6 | john 10.3.1.11 | john 08:00:27:a4:21:98   |

3. Accès internet
🌞Donnez un accès internet à vos machines

John:

GATEWAY=10.0.2.15
[oceane@localhost ~]$ sudo systemctl restart NetworkManager
[oceane@localhost ~]$ ping 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=112 time=23.0 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=112 time=24.7 ms
64 bytes from 8.8.8.8: icmp_seq=3 ttl=112 time=23.3 ms
64 bytes from 8.8.8.8: icmp_seq=4 ttl=112 time=24.4 ms
64 bytes from 8.8.8.8: icmp_seq=5 ttl=112 time=25.6 ms
64 bytes from 8.8.8.8: icmp_seq=6 ttl=112 time=29.4 ms
64 bytes from 8.8.8.8: icmp_seq=7 ttl=112 time=28.2 ms
^C
--- 8.8.8.8 ping statistics ---
7 packets transmitted, 7 received, 0% packet loss, time 6009ms
rtt min/avg/max/mdev = 23.003/25.495/29.362/2.249 ms
[oceane@localhost ~]$ sudo nano /etc/sysconfig/network
[oceane@localhost ~]$ sudo systemctl restart NetworkManager
[oceane@localhost ~]$ dig google.com

; <<>> DiG 9.16.23-RH <<>> google.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 52219
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 512
;; QUESTION SECTION:
;google.com.                    IN      A

;; ANSWER SECTION:
google.com.             300     IN      A       142.250.179.78

;; Query time: 29 msec
;; SERVER: 8.8.8.8#53(8.8.8.8)
;; WHEN: Fri Oct 28 12:14:13 CEST 2022
;; MSG SIZE  rcvd: 55

[oceane@localhost ~]$ ping google.com
PING google.com (142.250.179.78) 56(84) bytes of data.
64 bytes from par21s19-in-f14.1e100.net (142.250.179.78): icmp_seq=1 ttl=112 time=24.2 ms
64 bytes from par21s19-in-f14.1e100.net (142.250.179.78): icmp_seq=2 ttl=112 time=25.3 ms
64 bytes from par21s19-in-f14.1e100.net (142.250.179.78): icmp_seq=3 ttl=112 time=26.9 ms
^C
--- google.com ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2005ms
rtt min/avg/max/mdev = 24.246/25.491/26.899/1.089 ms
[oceane@localhost ~]$

Marcel:
GATEWAY=10.0.2.15
 sudo nano /etc/systconfig/network
[sudo] password for oceane:
[oceane@localhost ~]$ sudo nano /etc/systconfig/network
[oceane@localhost ~]$ cd
[oceane@localhost ~]$ ls
ifcfg-enp0s8
[oceane@localhost ~]$ sudo nano /etc/systconfig/network
[oceane@localhost ~]$ sudo nano /etc/sysconfig/network
[oceane@localhost ~]$ sudo systemctl restart NetworkManager
[oceane@localhost ~]$ ping 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=112 time=31.4 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=112 time=32.3 ms
64 bytes from 8.8.8.8: icmp_seq=3 ttl=112 time=28.7 ms
64 bytes from 8.8.8.8: icmp_seq=4 ttl=112 time=31.0 ms
^C
--- 8.8.8.8 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 2997ms
rtt min/avg/max/mdev = 28.724/30.842/32.338/1.322 ms
[oceane@localhost ~]$

🌞Analyse de trames

capturez le ping depuis john avec tcpdump

analysez un ping aller et le retour qui correspond et mettez dans un tableau :
```
[oceane@localhost ~]$ ping 8.8.8.8
sudo tcpdump -i enp0s8 -w tp3_accesinternet.pcap
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=112 time=23.8 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=112 time=24.6 ms
64 bytes from 8.8.8.8: icmp_seq=3 ttl=112 time=22.2 ms
^C
--- 8.8.8.8 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2004ms
rtt min/avg/max/mdev = 22.152/23.526/24.613/1.024 ms
[oceane@localhost ~]$
```
effectuez un ping 8.8.8.8 depuis john:


capturez le ping depuis john avec tcpdump:

[analyse de trammes](tp3_ping-google.com.pcap)


![](https://i.imgur.com/zYg2XbH.jpg)


## III. DHCP

1. Mise en place du serveur DHCP
🌞Sur la machine john, vous installerez et configurerez un serveur DHCP (go Google "rocky linux dhcp server").

Après avoir installé et configuré le serveur DHCP sur john, voici l'adresse IP  dynamique en DHCP de bob: 
192.168.56.104. 

Pour ce faire j'ai rentré ip a puis sudo nano /etc/sysconfig/network-scripts puis DEVICE=enp0s8

BOOTPROTO=dhcp
ONBOOT=yes
 et j'ai rentré le nom du fichier : /etc/sysconfig/network-scripts/ifcfg-enp0s8 
 
et j'ai reddémarrer l'interface:
sudo nmcli con reload
sudo nmcli con up "System enp0s8"
sudo systemctl restart NetworkManager puis ip a. 


🌞Améliorer la configuration du DHCP
Pour ajouter une route par default dans la configuration DHCP, dans la configuration DHCP directement j'ai mis cette commande : 
ip route add default via 10.3.1.254 dev enp0s8.

Pour ajouter le serveur DNS, j'ai mis cette commande : 
/etc/resolv.conf

BOB:
commande ip a pour de nouveau avoir l'adresse ip.