## TP4 : TCP, UDP et services réseau

#### I. First steps
🌞 Déterminez, pour ces 5 applications, si c'est du TCP ou de l'UDP

    Discord (appel)
    UDP
    IP serveur : 10.33.16.252
    port serveur :443
    port local :66 49 538

    Teams (message)
    TCP
     IP serveur : 10.33.16.252
    port serveur : 80
    port local : 49601

    Youtube (vidéo)
    UDP
     IP serveur: 10.33.16.252
    port seveur :57696
    port local : 68443

    GTA online
    UDP
     IP serveur : 10.33.16.249
    port serveur :  443
    port local :62446

    shein (site web)
    TCP
     IP serveur: 10.33.16.252
    port serveur : 53
    port local : 63276

 🌞 Demandez l'avis à votre OS
🦈🦈🦈🦈🦈 Bah ouais, captures Wireshark à l'appui évidemment. Une capture pour chaque application, qui met bien en évidence le trafic en question.

Discord

[capture de trammes](./Discord%20appel%20capture.pcapng)

Teams 

[capture de trammes](Teams%20(message)%20capture.pcapng)

Youtube 

[capture de trammes](Youtube%20vid%C3%A9o%20Capture.pcapng)

GTA 

[capture de trammes](capture%20de%20trames%20GTA%20Online.pcapng)

Shein 

[capture de trammes](Shein%20site%20wev%20capture.pcapng)


II. Mise en place
1. SSH
🌞 Examinez le trafic dans Wireshark

Le trafic ssh utilise le TCP et non l'UDP parce que l'UDP n'est pas fiable même s'il est rapide tandis que le TCP est fiable même s'il est lent. On préfère avoir une connexion fiable pour du SSH même s'il elle est lente plutôt qu'une connexion pas fiable même si elle est rapide. 

 🌞 Demandez aux OS
[commande : nestat -nb] (windows)
```
TCP 10.4.1.1: 52038   10.4.1.11:22  ESTABLISHED
[ssh.exe]
```
Sur VM:
````
[root@node1 ~]$ ss -tn
  password for root:

State   Recv-Q   Send-Q   Local Adress:     peer Adress:Port   Process
ESTAB      0      36       10.4.1.11:22     10.4.1.11:52038
````

🦈 Je veux une capture clean avec le 3-way handshake, un peu de trafic au milieu et une fin de connexion

[capture de trammes](II%20SSH%203%20way%20wireshark.pcapng)

III. DNS
🌞 Dans le rendu, je veux

-un cat des fichiers de conf
````
[root@dns-serveur ~]$  cat /etc/named.conf

options {
        listen-on port 53 { 127.0.0.1; any; };
        listen-on-v6 port 53 { ::1; };
        directory       "/var/named";
        dump-file       "/var/named/data/cache_dump.db";
        statistics-file "/var/named/data/named_stats.txt";
        memstatistics-file "/var/named/data/named_mem_stats.txt";
        secroots-file   "/var/named/data/named.secroots";
        recursing-file  "/var/named/data/named.recursing";
        allow-query     { localhost; any; };
        allow-query-cache { localhost; any; };
        /*
         - If you are building an AUTHORITATIVE DNS server, do NOT enable recursion.
         - If you are building a RECURSIVE (caching) DNS server, you need to enable
           recursion.
         - If your recursive DNS server has a public IP address, you MUST enable access
           control to limit queries to your legitimate users. Failing to do so will
           cause your server to become part of large scale DNS amplification
           attacks. Implementing BCP38 within your network would greatly
           reduce such attack surface
        */
        recursion yes;
        dnssec-validation yes;
        managed-keys-directory "/var/named/dynamic";
        geoip-directory "/usr/share/GeoIP";
        pid-file "/run/named/named.pid";
        session-keyfile "/run/named/session.key";
        /* https://fedoraproject.org/wiki/Changes/CryptoPolicy */
        include "/etc/crypto-policies/back-ends/bind.config";
};
logging {
        channel default_debug {
                file "data/named.run";
                severity dynamic;
        };
};
# référence vers notre fichier de zone
zone "tp4.b1" IN {
     type master;
     file "tp4.b1.db";
     allow-update { none; };
     allow-query {any; };
};
# référence vers notre fichier de zone inverse
zone "1.4.10.in-addr.arpa" IN {
     type master;
     file "tp4.b1.rev";
     allow-update { none; };
     allow-query { any; };
};
````


-Un systemctl status named qui prouve que le service tourne bien
````

[root@dns-server /]$ systemctl status named
● named.service - Berkeley Internet Name Domain (DNS)
     Loaded: loaded (/usr/lib/systemd/system/named.service; enabled; vendor preset: disabled)
     Active: active (running) since Tue 2022-10-25 13:45:12 CEST; 33s ago
````   

-Une commande ss qui prouve que le service écoute bien sur un port
````
[root@dns-server etc]$  ss -alntp
State      Recv-Q     Send-Q         Local Address:Port          Peer Address:Port     Process
LISTEN     0          10                10.4.1.201:53                 0.0.0.0:*         users:(("named",pid=1552,fd=21))
````

🌞 Ouvrez le bon port dans le firewall

Grâce à la commande ss vous devrez avoir repéré sur quel port tourne le service: Le port 53
````
[root@dns-serveur etc]$ firewall-cmd --add-port=53/udp --permanent
success
[root@dns-serveur etc]$  firewall-cmd --reload
success
````

3. Test

🌞 Sur la machine node1.tp4.b1
````
[root@node1 ~]$ dig dns-server.tp4.b1

  DiG 9.16.23-RH  dns-server.tp4.b1
 global options: +cmd
Got answer:
  opcode: QUERY, status: NOERROR, id: 57615
 flags: qr aa rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

 OPT PSEUDOSECTION:
  EDNS: version: 0, flags:; udp: 1232
 COOKIE: 9b711f49ccabe622010000006357e7290c5df5bd63baa1ad (good)
 QUESTION SECTION:
dns-server.tp4.b1.             IN      A

 ANSWER SECTION:
dns-server.tp4.b1.      86400   IN      A       10.4.1.201

 Query time: 1 msec
 SERVER: 10.4.1.201#53(10.4.1.201)
 WHEN: Tue Oct 25 16:22:03 CEST 2022
 MSG SIZE  rcvd: 90
[root@node1 ~]$ dig google.com

 DiG 9.16.23-RH  google.com
 global options: +cmd
 Got answer:
  opcode: QUERY, status: NOERROR, id: 33888
 flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

 OPT PSEUDOSECTION:
EDNS: version: 0, flags:; udp: 1232
COOKIE: bd516d4a8c0468ab010000006357e702a93ad2652cdcb9a2 
 QUESTION SECTION:
google.com.                    IN      A

 ANSWER SECTION:
google.com.             136     IN      A       216.58.214.174

 Query time: 0 msec
 SERVER: 10.4.1.201#53(10.4.1.201)
WHEN: Tue Oct 25 16:21:27 CEST 2022
 MSG SIZE  rcvd: 83
````
🌞 Sur votre PC

PS C:\Windows\system32> nslookup node1.tp4.b1 10.4.1.201
Serveur :   dns-server.tp4.b1
Address:  10.4.1.201

Nom :    node1.tp4.b1
Address:  10.4.1.11


🦈 Capture d'une requête DNS vers le nom node1.tp4.b1 ainsi que la réponse


[capture DNS](./DNS.pcapng)