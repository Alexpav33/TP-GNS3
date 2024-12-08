# TP1 : Remise dans le bain
**I. Most simplest LAN**

🌞 **Déterminer l'adresse MAC de vos deux machines**

````
show
````

🌞 **Définir une IP statique sur les deux machines**

Clique droit sur une machine « edit config » puis enlever le # pour qu’il soit pas en commentaire et mettre la bonne adresse IP.

````
# This the configuration for node1.tp1.efrei

#

# Uncomment the following line to enable DHCP

# dhcp

# or the line below to manually setup an IP address and subnet mask

 ip 10.1.1.1 255.255.255.0

#

set pcname node1.tp1.efrei
````


````
show ip
````

🌞 **Effectuer un `ping` d'une machine à l'autre**

- c'est la commande `ping`, sans surprise vous me direz CA MARCHE
````
VPCS> ping 10.1.1.2
84 bytes from 10.1.1.2 icmp_seq=1 ttl=64 time=0.649 ms
84 bytes from 10.1.1.2 icmp_seq=2 ttl=64 time=0.852 ms
84 bytes from 10.1.1.2 icmp_seq=3 ttl=64 time=0.705 ms
84 bytes from 10.1.1.2 icmp_seq=4 ttl=64 time=1.421 ms
84 bytes from 10.1.1.2 icmp_seq=5 ttl=64 time=1.676 ms

VPCS>
````

🌞 **Wireshark !**

Le protocole utilisé pour envoyer le message `ping` est ICMP (Internet Control Message Protocol).

[Capture Wireshark (Étape 1)](étape1.pcapng)

🌞 **ARP**

 Commande depuis `node1.tp1.efrei` pour connaître la MAC de son correspondant `node2.tp1.efrei` : 
 ````
 arp

00:50:79:66:68:00  10.1.1.2 expires in 54 seconds
````


🌞 **Déterminer l'adresse MAC de vos trois machines**

Utilisation de la commande **"show"**

node1.tp1.efrei :  00:50:79:66:68:02

node2.tp1.efrei :  00:50:79:66:68:01

node3.tp1.efrei :  00:50:79:66:68:00

🌞 **Définir une IP statique sur les trois machines**

- prouver que votre changement d'IP est effectif, en une commande : 
````
show ip 
````

🌞 **Effectuer des `ping` d'une machine à l'autre**

- vérifiez que tout le monde peut se joindre : 

  - `node1` à `node2` :

````
 ping 10.1.1.2
84 bytes from 10.1.1.2 icmp_seq=1 ttl=64 time=0.694 ms
84 bytes from 10.1.1.2 icmp_seq=2 ttl=64 time=0.526 ms
84 bytes from 10.1.1.2 icmp_seq=3 ttl=64 time=1.772 ms
84 bytes from 10.1.1.2 icmp_seq=4 ttl=64 time=1.076 ms
84 bytes from 10.1.1.2 icmp_seq=5 ttl=64 time=0.729 ms

VPCS>
````

  - `node2` à `node3`

````
ping 10.1.1.3
84 bytes from 10.1.1.3 icmp_seq=1 ttl=64 time=0.962 ms
84 bytes from 10.1.1.3 icmp_seq=2 ttl=64 time=0.868 ms
84 bytes from 10.1.1.3 icmp_seq=3 ttl=64 time=0.245 ms
84 bytes from 10.1.1.3 icmp_seq=4 ttl=64 time=0.374 ms
84 bytes from 10.1.1.3 icmp_seq=5 ttl=64 time=1.678 ms
````

  - `node1` à `node3`

````
 ping 10.1.1.3
84 bytes from 10.1.1.3 icmp_seq=1 ttl=64 time=0.928 ms
84 bytes from 10.1.1.3 icmp_seq=2 ttl=64 time=1.202 ms
84 bytes from 10.1.1.3 icmp_seq=3 ttl=64 time=0.917 ms
84 bytes from 10.1.1.3 icmp_seq=4 ttl=64 time=1.445 ms
84 bytes from 10.1.1.3 icmp_seq=5 ttl=64 time=1.149 ms

VPCS>
````

**III. Serveur DHCP**

🌞 **Donner un accès Internet à la machine `dhcp.tp1.efrei`**

Commande que vous avez un accès internet : 
````
ping -c google.com
````
🌞 **Installer et configurer un serveur DHCP**

Commande pour configurer un serveur DHCP : 

````
sudo dnf install -y dhcp-serveur

sudo nano /etc/dhcp/dhcpd.conf

authoritative;
subnet 10.1.1.0 netmask 255.255.255.0 {
    range 10.1.1.10 10.1.1.50;
    option broadcast-address 10.1.1.1;
    option routers 10.1.1.1;
}

sudo systemctl enabled dhcpd
sudo systemctl start dhcpd
sudo systemctl status dhcpd
````
Active (running)

🌞 **Récupérer une IP automatiquement depuis les 3 nodes**
Changement d'IP est effectif, en une commande : 
````
ip dhcp
````
node1.tp1.efrei : 10.1.1.10
node2.tp1.efrei : 10.1.1.12
node3.tp1.efrei : 10.1.1.11

🌞 **Wireshark !**

[Capture Wireshark (DORA)](wireshark%20dora.pcapng)

🌞 **Configurez dnsmasq**

````
sudo dnf install dnsmasq -y

port=0 # pour désactiver DNS
dhcp-range=10.1.1.210,10.1.1.250,255.255.255.0,12h
dhcp-authoritative
interface=enp0s3
````


authoritative;
subnet 10.1.1.0 netmask 255.255.255.0 {
    range 10.1.1.210 10.1.1.250;
    option broadcast-address 10.1.1.1;
    option routers 10.1.1.1;
}

````

🌞 **Test !**

PC -> dhcp
DDORA IP 10.1.1.248
24 GW 10.1.1.50
````
````
PC -> show ip

NAME        : PC4[1]
IP/MASK     : 10.1.1.248/24
````

🌞 **Now race !**

machine 2 -> dhcp

DDORA IP 10.1.1.246/24 GW 10.1.1.50

Machine 3 -> dhcp

DDORA IP 10.1.1.247/24 GW 10.1.1.50

Machine 4 -> dhcp

DORA IP 10.1.1.21/24 GW 10.1.1.1




