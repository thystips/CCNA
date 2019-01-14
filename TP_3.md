# TP 3

## 4. Configuration réseau d'une machine CentOS

### _a)_ utilisez une commande pour prouver que vous avez internet depuis la VM

Pour cela on peut utiliser plusieurs commandes différentes dont "dig" et "curl" parmis d'autres :

```bash

[root@centos /]# curl google.fr && dig google.fr

<HTML><HEAD><meta http-equiv="content-type" content="text/html;charset=utf-8">

<TITLE>301 Moved</TITLE></HEAD><BODY>

<H1>301 Moved</H1>

The document has moved

<A HREF="http://www.google.fr/">here</A>.

</BODY></HTML>

  

; <<>> DiG 9.9.4-RedHat-9.9.4-72.el7 <<>> google.fr

;; global options: +cmd

;; Got answer:

;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 20288

;; flags: qr rd ad; QUERY: 1, ANSWER: 11, AUTHORITY: 0, ADDITIONAL: 0

;; WARNING: recursion requested but not available

  

;; QUESTION SECTION:

;google.fr. IN A

  

;; ANSWER SECTION:

google.fr. 0 IN A 216.58.208.195

e.ext.nic.fr. 0 IN A 193.176.144.22

e.ext.nic.fr. 0 IN AAAA 2a00:d78:0:102:193:176:144:22

f.ext.nic.fr. 0 IN A 194.146.106.46

f.ext.nic.fr. 0 IN AAAA 2001:67c:1010:11::53

g.ext.nic.fr. 0 IN A 194.0.36.1

g.ext.nic.fr. 0 IN AAAA 2001:678:4c::1

d.nic.fr. 0 IN A 194.0.9.1

d.nic.fr. 0 IN AAAA 2001:678:c::1

d.ext.nic.fr. 0 IN A 192.5.4.2

d.ext.nic.fr. 0 IN AAAA 2001:500:2e::2

  

;; Query time: 1 msec

;; SERVER: 172.17.190.33#53(172.17.190.33)

;; WHEN: mar. janv. 08 16:32:50 CET 2019

;; MSG SIZE rcvd: 328

```

"&&" permet de combiner plusieurs commandes en une seule ligne

### _b)_ prouvez que votre PC hôte et la VM peuvent communiquer

#### En utilisant la commande "Ping"

##### VM vers Windows

```bash
[root@centos /]# ping -c 5 192.168.127.1
PING 192.168.127.1 (192.168.127.1) 56(84) bytes of data.
64 bytes from 192.168.127.1: icmp_seq=1 ttl=128 time=0.522 ms
64 bytes from 192.168.127.1: icmp_seq=2 ttl=128 time=0.495 ms
64 bytes from 192.168.127.1: icmp_seq=3 ttl=128 time=0.468 ms
64 bytes from 192.168.127.1: icmp_seq=4 ttl=128 time=0.641 ms
64 bytes from 192.168.127.1: icmp_seq=5 ttl=128 time=0.493 ms
```

##### Windows vers VM

```powershell

PS C:\Users\contact> ping 192.168.127.10

Envoi d’une requête 'Ping'  192.168.127.10 avec 32 octets de données:
Réponse de 192.168.127.10: octets=32 temps<1ms TTL=64
Réponse de 192.168.127.10: octets=32 temps<1ms TTL=64
Réponse de 192.168.127.10: octets=32 temps<1ms TTL=64
Réponse de 192.168.127.10: octets=32 temps<1ms TTL=64

Statistiques Ping pour 192.168.127.10:
    Paquets: envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 0ms, Maximum = 0ms, Moyenne = 0ms

```

### _c)_ affichez votre table de routage **sur la VM** et expliquez chacune des lignes

```bash
[root@centos /]# ip route
default via 10.0.2.2 dev enp0s3 proto dhcp metric 101
10.0.2.0/24 dev enp0s3 proto kernel scope link src 10.0.2.15 metric 101
192.168.127.0/24 dev enp0s8 proto kernel scope link src 192.168.127.10 metric 100
```

#### Explications ligne par ligne

```bash
default via 10.0.2.2 dev enp0s3 proto dhcp metric 100
```

_Cette ligne définie la carte réseau permettant d'accéder au réseaux auquels la machine n'est pas directement connectés, la carte qui renvoit à la passerelle. Ici "dhcp" signifie que la passerelle utilisé est celle donnée automatiquement par le serveur dhcp._

```bash
10.0.2.0/24 dev enp0s3 proto kernel scope link src 10.0.2.15 metric 101
```

_Cette ligne elle indique à la machine que pour accéder à ce précis (ici 10.0.2.0/24) il faut utiliser le lien (carte réseau physique, carte réseau virtuelle ou autre dans certains cas) ayant telle adresse IP (ici 10.0.2.15)._

```bash
192.168.127.0/24 dev enp0s8 proto kernel scope link src 192.168.127.10 metric 100
```

_Là il s'agit de la même chose que la ligne précedente mais pour le réseau 192.168.127.0/24 via l'adresse 192.168.127.10._

#### Ci-dessous une requête "ping" de l'hôte vers la VM

```powershell
PS C:\Users\mathc> ping 192.168.127.10

Envoi d’une requête 'Ping'  192.168.127.10 avec 32 octets de données:
Réponse de 192.168.127.10: octets=32 temps<1ms TTL=64
Réponse de 192.168.127.10: octets=32 temps<1ms TTL=64
Réponse de 192.168.127.10: octets=32 temps<1ms TTL=64
Réponse de 192.168.127.10: octets=32 temps<1ms TTL=64

Statistiques Ping pour 192.168.127.10:
    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 0ms, Maximum = 0ms, Moyenne = 0ms
```

#### Et ci-dessous une requête "ping" de la VM vers l'hôte

```bash
[root@centos /]# ping -c 5 192.168.127.1
PING 192.168.127.1 (192.168.127.1) 56(84) bytes of data.
64 bytes from 192.168.127.1: icmp_seq=1 ttl=128 time=0.522 ms
64 bytes from 192.168.127.1: icmp_seq=2 ttl=128 time=0.495 ms
64 bytes from 192.168.127.1: icmp_seq=3 ttl=128 time=0.468 ms
64 bytes from 192.168.127.1: icmp_seq=4 ttl=128 time=0.641 ms
64 bytes from 192.168.127.1: icmp_seq=5 ttl=128 time=0.493 ms
```

#### Pour continuer la table de routage de l'hôte avec la commande "route PRINT"

```powershell
PS C:\Users\mathc> route PRINT -4
===========================================================================
Liste d'Interfaces
  12...0c 9d 92 39 97 6e ......Realtek PCIe GBE Family Controller
  47...0a 00 27 00 00 2f ......VirtualBox Host-Only Ethernet Adapter #2
  9...30 24 32 3c 63 ca ......Microsoft Wi-Fi Direct Virtual Adapter
  3...32 24 32 3c 63 c9 ......Microsoft Wi-Fi Direct Virtual Adapter #2
  8...30 24 32 3c 63 c9 ......Intel(R) Wireless-AC 9560
  1...........................Software Loopback Interface 1
===========================================================================

IPv4 Table de routage
===========================================================================
Itinéraires actifs :
Destination réseau    Masque réseau  Adr. passerelle   Adr. interface Métrique
          0.0.0.0          0.0.0.0      10.33.3.253      10.33.1.191     35
        10.33.0.0    255.255.252.0         On-link       10.33.1.191    291
      10.33.1.191  255.255.255.255         On-link       10.33.1.191    291
      10.33.3.255  255.255.255.255         On-link       10.33.1.191    291
        127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
        127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
  127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
    192.168.127.0    255.255.255.0         On-link     192.168.127.1    330
    192.168.127.1  255.255.255.255         On-link     192.168.127.1    330
  192.168.127.255  255.255.255.255         On-link     192.168.127.1    330
        224.0.0.0        240.0.0.0         On-link         127.0.0.1    331
        224.0.0.0        240.0.0.0         On-link       10.33.1.191    291
        224.0.0.0        240.0.0.0         On-link     192.168.127.1    330
  255.255.255.255  255.255.255.255         On-link         127.0.0.1    331
  255.255.255.255  255.255.255.255         On-link       10.33.1.191    291
  255.255.255.255  255.255.255.255         On-link     192.168.127.1    330
===========================================================================
Itinéraires persistants :
  Aucun
```

#### Téléchargement d'un fichier avec wget

```bash
[root@centos /]# wget https://github.com/It4lik/B1-Reseau-2018/blob/master/README.md
--2019-01-08 17:47:37--  https://github.com/It4lik/B1-Reseau-2018/blob/master/README.md
Résolution de github.com (github.com)... 140.82.118.3, 140.82.118.4, 192.5.6.30, ...
Connexion vers github.com (github.com)|140.82.118.3|:443...connecté.
requête HTTP transmise, en attente de la réponse...200 OK
Longueur: non spécifié [text/html]
Sauvegarde en : "README.md"

    [ <=>                                                                                                 ] 54 302      --.-K/s   ds 0,1s

2019-01-08 17:47:37 (380 KB/s) - "README.md" sauvegardé [54302]
```

#### Utilisation de la commande "dig"

La commande "dig" permet d'obtenir les

```bash
[root@centos antoinethys]# dig ynov.com && dig google.com

; <<>> DiG 9.9.4-RedHat-9.9.4-72.el7 <<>> ynov.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 37738
;; flags: qr rd ad; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 0
;; WARNING: recursion requested but not available

;; QUESTION SECTION:
;ynov.com.                      IN      A

;; ANSWER SECTION:
ynov.com.               0       IN      A       217.70.184.38

;; Query time: 2 msec
;; SERVER: 172.17.190.33#53(172.17.190.33)
;; WHEN: mar. janv. 08 21:03:19 CET 2019
;; MSG SIZE  rcvd: 50


; <<>> DiG 9.9.4-RedHat-9.9.4-72.el7 <<>> google.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 7793
;; flags: qr rd ad; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 0
;; WARNING: recursion requested but not available

;; QUESTION SECTION:
;google.com.                    IN      A

;; ANSWER SECTION:
google.com.             0       IN      A       216.58.204.238

;; Query time: 1 msec
;; SERVER: 172.17.190.33#53(172.17.190.33)
;; WHEN: mar. janv. 08 21:03:19 CET 2019
;; MSG SIZE  rcvd: 54

```

## Exploration des ports locaux

  Commande SS:
* Ports TCP IPv4 en écoute:

```bash
[root@centos ~]# ss -l -t -4
State      Recv-Q Send-Q                        Local Address:Port                                         Peer Address:Port
LISTEN     0      128                                       *:ssh                                                     *:*
```

* Applications écoutant sur les ports TCP IPv4:

```bash
[root@centos ~]# ss -l -t -4 -p -n
State      Recv-Q Send-Q                          Local Address:Port                                         Peer Address:Port
LISTEN     0      128                                         *:22                                                      *:*
  users:(("sshd",pid=3075,fd=3))
```

L'application sshd (le serveur SSH) écoute sur le port ssh (port 22).

## SSH

![SSH avec Token2Shell](https://github.com/antoine33520/CCNA/blob/master/TP3/SSH_Token2Shell.png?raw=True)

Pour la connexion SSH j'ai utilisé le client Token2Shell de Choung Networks, il s'agit d'un client multifonction et très puissant, il support SSH, Telnet, TCP Direct, Bluetooth, Serial et l'accès à Docker soit par l'accès à un container existant soit par la création d'un nouveau. Toutes les fonctions peuvent être gérées graphiquement (clés privées, répertoire, macro de commandes, agent ssh, x11, etc...). La société propose également un serveur X très complet (X410) ainsi qu'une version de linux pour WSL (WLinux).

## Firewall

### A faire

* Changement de port :

```bash
[root@centos ~]# nano /etc/ssh/sshd_config
```

```bash
Port 22
```

Devient

```bash
Port 2266
```

* Résultat de la commende ```ss -l -t -4 -n``` :

```bash
[root@centos ~]# ss -t -l -4 -n
State      Recv-Q Send-Q                          Local Address:Port                                         Peer Address:Port
LISTEN     0      128                                         *:2266                                                    *:*
```

* Erreur de connexion :

Lors de la prémière tentative de connexion il y a une erreur. Ce probème est dû à un bloquage du port 2266 qui n'a pas été autorisé par le pare-feu.
La solution est donc de faire une entrée manuelle dans la pare-feu:

```bash
firewall-cmd --add-port=266/tcp --permanent
```