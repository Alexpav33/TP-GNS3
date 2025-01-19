🌞 **`ping` d'un client du  `LAN1` vers un client du `LAN 2`**

````

PC1> ping 10.3.2.1

10.3.2.1 icmp_seq=1 timeout
84 bytes from 10.3.2.1 icmp_seq=2 ttl=62 time=26.984 ms
84 bytes from 10.3.2.1 icmp_seq=3 ttl=62 time=37.523 ms
84 bytes from 10.3.2.1 icmp_seq=4 ttl=62 time=27.703 ms
84 bytes from 10.3.2.1 icmp_seq=5 ttl=62 time=43.723 ms

PC1>
PC1>
````

🌞 **Capture Wireshark `ping_partie1`**

🌞 **Afficher les adresses MAC des routeurs**

````
R2#show arp
Protocol  Address          Age (min)  Hardware Addr   Type   Interface
Internet  10.3.12.1              43   c401.051e.0010  ARPA   FastEthernet0/0
Internet  10.3.12.2               -   c402.053c.0000  ARPA   FastEthernet0/0
Internet  10.3.2.2               16   0050.7966.6803  ARPA   FastEthernet0/1
Internet  10.3.2.1               10   0050.7966.6802  ARPA   FastEthernet0/1
Internet  10.3.2.254              -   c402.053c.0001  ARPA   FastEthernet0/1
R2#
````

### C. Accès internet

🌞 **Prouvez que vous avez déjà un accès internet sur `r1`**

➜ **Configurer `r1` pour qu'il fasse du NAT**

````
R1#ping 1.1.1.1

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 1.1.1.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 40/43/52 ms
R1#
R1#
````

🌞 **Accès internet `LAN1`**

```
PC1> ping 8.8.8.8

184 bytes from 8.8.8.8 icmp_seq=1 ttl=254 time=19.417 ms
184 bytes from 8.8.8.8 icmp_seq=2 ttl=254 time=52.280 ms
184 bytes from 8.8.8.8 icmp_seq=3 ttl=254 time=21.102 ms
```

🌞 **Accès internet `LAN2`**
```
PC3> ping 1.1.1.1

84 bytes from 1.1.1.1 icmp_seq=1 ttl=254 time=34.924 ms
84 bytes from 1.1.1.1 icmp_seq=2 ttl=254 time=31.382 ms
84 bytes from 1.1.1.1 icmp_seq=3 ttl=254 time=42.789 ms
84 bytes from 1.1.1.1 icmp_seq=4 ttl=254 time=43.021 ms
```

# Partie II. Router-on-a-stick

## 2. Tableau d'adressage

### A. Réseaux

| Réseau | Adresse Réseau | VLAN |
| ------ | -------------- | ---- |
| `LAN1` | `10.3.1.0/24`  | 10   |
| `LAN2` | `10.3.2.0/24`  | 20   |
| `LAN3` | `10.3.3.0/24`  | 30   |

### B. Tableau adressage

| Machine       | `LAN1`       | `LAN2`       | `LAN3`       |
| ------------- | ------------ | ------------ | ------------ |
| `pc1.tp3.b2`  | `10.3.1.1`   | x            | x            |
| `pc2.tp3.b2`  | x            | `10.3.2.1`   | x            |
| `pc3.tp3.b2`  | `10.3.1.2`   | x            | x            |
| `pc4.tp3.b2`  | x            | DHCP         | x            |
| `dhcp.tp3.b2` | x            | `10.3.2.253` | x            |
| `r1.tp3.b2`   | `10.3.1.254` | `10.3.2.254` | `10.3.3.254` |
| `dns.tp3.b2`  | x            | x            | `10.3.3.1`   |
| `web.tp3.b2`  | x            | x            | `10.3.3.2`   |

### A. VLANs

🌞 **Tests de `ping`**

`PC 1 vers PC 3`
````
PC1> ping 10.3.1.2

84 bytes from 10.3.1.2 icmp_seq=1 ttl=64 time=0.662 ms
84 bytes from 10.3.1.2 icmp_seq=2 ttl=64 time=1.156 ms
84 bytes from 10.3.1.2 icmp_seq=3 ttl=64 time=0.825 ms
84 bytes from 10.3.1.2 icmp_seq=4 ttl=64 time=1.262 ms
84 bytes from 10.3.1.2 icmp_seq=5 ttl=64 time=0.873 ms

````
`PC 2 vers PC 4`

````
PC2> ping 10.3.2.2

84 bytes from 10.3.2.2 icmp_seq=1 ttl=64 time=0.569 ms
84 bytes from 10.3.2.2 icmp_seq=2 ttl=64 time=0.738 ms
84 bytes from 10.3.2.2 icmp_seq=3 ttl=64 time=1.050 ms
84 bytes from 10.3.2.2 icmp_seq=4 ttl=64 time=0.674 ms
84 bytes from 10.3.2.2 icmp_seq=5 ttl=64 time=1.874 ms

````

### B. Routeur

🌞 **Tests de `ping`**

````
PC2> sh ip

NAME        : PC2[1]
IP/MASK     : 10.3.2.1/24
GATEWAY     : 10.3.2.254
DNS         :
MAC         : 00:50:79:66:68:01
LPORT       : 20020
RHOST:PORT  : 127.0.0.1:20021
MTU         : 1500

PC2> ping 10.3.1.1

84 bytes from 10.3.1.1 icmp_seq=1 ttl=63 time=20.011 ms
84 bytes from 10.3.1.1 icmp_seq=2 ttl=63 time=13.141 ms
84 bytes from 10.3.1.1 icmp_seq=3 ttl=63 time=27.810 ms
84 bytes from 10.3.1.1 icmp_seq=4 ttl=63 time=12.692 ms
84 bytes from 10.3.1.1 icmp_seq=5 ttl=63 time=15.577 ms

````

➜ **Configuration du NAT**

🌞 **Tests de `ping`**

````
R1#ping 1.1.1.1

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 1.1.1.1, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 68/87/96 ms
````
````
PC1> ping 1.1.1.1

84 bytes from 1.1.1.1 icmp_seq=1 ttl=58 time=35.015 ms
84 bytes from 1.1.1.1 icmp_seq=2 ttl=58 time=36.085 ms
84 bytes from 1.1.1.1 icmp_seq=3 ttl=58 time=41.733 ms
84 bytes from 1.1.1.1 icmp_seq=4 ttl=58 time=53.997 ms
84 bytes from 1.1.1.1 icmp_seq=5 ttl=58 time=31.964 ms
````

# III. Services dans le LAN

## 1. DHCP

🌞 **Prouvez avec un VPCS**

````
PC2> ip dhcp
DDORA IP 10.3.2.50/24 GW 10.3.2.254

PC2> sh ip

NAME        : PC2[1]
IP/MASK     : 10.3.2.50/24
GATEWAY     : 10.3.2.254
DNS         : 1.1.1.1
DHCP SERVER : 10.3.2.253
DHCP LEASE  : 43195, 43200/21600/37800
MAC         : 00:50:79:66:68:02
LPORT       : 20023
RHOST:PORT  : 127.0.0.1:20024
MTU         : 1500

PC2> ping www.efrei.fr
www.efrei.fr resolved to 51.255.68.208

84 bytes from 51.255.68.208 icmp_seq=1 ttl=51 time=64.372 ms
84 bytes from 51.255.68.208 icmp_seq=2 ttl=51 time=60.753 ms
84 bytes from 51.255.68.208 icmp_seq=3 ttl=51 time=72.137 ms
84 bytes from 51.255.68.208 icmp_seq=4 ttl=51 time=75.154 ms
````

## 2. DNS

🌞 **Tests résolutions DNS**

````
PC4> ip dhcp
DORA IP 10.3.2.51/24 GW 10.3.2.254

PC4> sh ip

NAME        : PC4[1]
IP/MASK     : 10.3.2.51/24
GATEWAY     : 10.3.2.254
DNS         : 10.3.3.2
DHCP SERVER : 10.3.2.253
DHCP LEASE  : 41259, 41261/20630/36103
MAC         : 00:50:79:66:68:03
LPORT       : 20027
RHOST:PORT  : 127.0.0.1:20028
MTU         : 1500

PC4> ping dns.tp3.b2
dns.tp3.b2 resolved to 10.3.3.2

84 bytes from 10.3.3.2 icmp_seq=1 ttl=63 time=19.311 ms
84 bytes from 10.3.3.2 icmp_seq=2 ttl=63 time=15.388 ms
^C
PC4> ping efrei.fr
efrei.fr resolved to 51.255.68.208

84 bytes from 51.255.68.208 icmp_seq=1 ttl=51 time=63.298 ms
^C
````

🌞 **Capture Wireshark**

## 3. HTTP

🌞 **Preuve avec un client**

````
PC4> ping web.tp3.b2
web.tp3.b2 resolved to 10.3.3.3

84 bytes from 10.3.3.3 icmp_seq=1 ttl=63 time=20.700 ms
84 bytes from 10.3.3.3 icmp_seq=2 ttl=63 time=18.845 ms
^C
PC4> ping supersite.tp3.b2
supersite.tp3.b2 resolved to 10.3.3.3

84 bytes from 10.3.3.3 icmp_seq=1 ttl=63 time=20.394 ms
84 bytes from 10.3.3.3 icmp_seq=2 ttl=63 time=16.646 ms

````

`curl :`
````
[alexa@localhost]$ curl -s web.tp3.b2 | head -n 10

<!doctype html>
<html>
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>HTTP Server Test Page powered by: Rocky Linux</title>
<style type="text/css">
/*<![CDATA[*/
    html {
````


