# **TP2 : Ethernet, IP, et ARP**

# Sommaire

- [TP2 : Ethernet, IP, et ARP](#tp2--ethernet-ip-et-arp)
- [Sommaire](#sommaire)
- [I. Setup IP](#i-setup-ip)
- [II. ARP my bro](#ii-arp-my-bro)
- [II.5 Interlude hackerzz](#ii5-interlude-hackerzz)
- [III. DHCP you too my brooo](#iii-dhcp-you-too-my-brooo)

# I. Setup IP

🌞 **Mettez en place une configuration réseau fonctionnelle entre les deux machines**

My pc : 10.10.10.2  
Quentin's PC : 10.10.10.1  
Network adress : 10.10.8.0  

I used ifconfig to modify my IP : `sudo ifconfig enx00e04c680033 10.10.10.2 netmask 255.255.252.0`  

🌞 **Prouvez que la connexion est fonctionnelle entre les deux machines**

```
nathan@nathan-SSD-Linux:~$ ping 10.10.10.1
PING 10.10.10.1 (10.10.10.1) 56(84) bytes of data.
64 bytes from 10.10.10.1: icmp_seq=1 ttl=128 time=1.33 ms
64 bytes from 10.10.10.1: icmp_seq=2 ttl=128 time=1.36 ms
64 bytes from 10.10.10.1: icmp_seq=3 ttl=128 time=1.50 ms
64 bytes from 10.10.10.1: icmp_seq=4 ttl=128 time=1.57 ms
^C
--- 10.10.10.1 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3005ms
rtt min/avg/max/mdev = 1.326/1.440/1.574/0.101 ms
```

🌞 **Wireshark it**

*File can be found in le dossier **[assets](./assets/ping.pcapng)***  
Echo request : 8  
Echo reply : 0  

# II. ARP my bro

🌞 **Check the ARP table**

For my ethernet interface :
```
nathan@nathan-SSD-Linux:~$ arp
Address                  HWtype  HWaddress           Flags Mask            Iface
10.10.10.1               ether   d8:bb:c1:f4:60:61   C                     enx00e04c680033
```
For my Wifi interface :
```
nathan@nathan-SSD-Linux:~$ arp
Address                  HWtype  HWaddress           Flags 
_gateway                 ether   00:c0:e7:e0:04:4e   C                     wlp0s20f3
```

🌞 **Manipuler la table ARP**

To clear my arp table I have used : `ip -s -s neigh flush all`  

After that (before a ping) : 
```
nathan@nathan-SSD-Linux:~$ arp
Address                  HWtype  HWaddress           Flags Mask            Iface
_gateway                 ether   00:c0:e7:e0:04:4e   C                     wlp0s20f3
```
After that (after a ping) : 
```
nathan@nathan-SSD-Linux:~$ arp
Address                  HWtype  HWaddress           Flags Mask            Iface
10.10.10.1               ether   d8:bb:c1:f4:60:61   C                     enx00e04c680033
_gateway                 ether   00:c0:e7:e0:04:4e   C                     wlp0s20f3
```

> Les échanges ARP sont effectuées automatiquement par votre machine lorsqu'elle essaie de joindre une machine sur le même LAN qu'elle. Si la MAC du destinataire n'est pas déjà dans la table ARP, alors un échange ARP sera déclenché.

🌞 **Wireshark it**

*File can be found in **[assets](./assets/ArpTest.pcapng)***  
Source : IP : `10.10.10.2`, MAC : `d8:bb:c1:f4:60:61`  
Destination : IP : `10.10.10.1`, MAC : `00:e0:4c:68:00:33`

# (II.I Interlude hackerzz)

**Chose promise chose due, on va voir les bases de l'usurpation d'identité en réseau : on va parler d'*ARP poisoning*.**

> On peut aussi trouver *ARP cache poisoning* ou encore *ARP spoofing*, ça désigne la même chose.

Le principe est simple : on va "empoisonner" la table ARP de quelqu'un d'autre.  
Plus concrètement, on va essayer d'introduire des fausses informations dans la table ARP de quelqu'un d'autre.

Entre introduire des fausses infos et usurper l'identité de quelqu'un il n'y a qu'un pas hihi.

---

➜ **Le principe de l'attaque**

- on admet Alice, Bob et Eve, tous dans un LAN, chacun leur PC
- leur configuration IP est ok, tout va bien dans le meilleur des mondes
- **Eve 'lé pa jonti** *(ou juste un agent de la CIA)* : elle aimerait s'immiscer dans les conversations de Alice et Bob
  - pour ce faire, Eve va empoisonner la table ARP de Bob, pour se faire passer pour Alice
  - elle va aussi empoisonner la table ARP d'Alice, pour se faire passer pour Bob
  - ainsi, tous les messages que s'envoient Alice et Bob seront en réalité envoyés à Eve

➜ **La place de ARP dans tout ça**

- ARP est un principe de question -> réponse (broadcast -> *reply*)
- IL SE TROUVE qu'on peut envoyer des *reply* à quelqu'un qui n'a rien demandé :)
- il faut donc simplement envoyer :
  - une trame ARP reply à Alice qui dit "l'IP de Bob se trouve à la MAC de Eve" (IP B -> MAC E)
  - une trame ARP reply à Bob qui dit "l'IP de Alice se trouve à la MAC de Eve" (IP A -> MAC E)
- ha ouais, et pour être sûr que ça reste en place, il faut SPAM sa mum, genre 1 reply chacun toutes les secondes ou truc du genre
  - bah ui ! Sinon on risque que la table ARP d'Alice ou Bob se vide naturellement, et que l'échange ARP normal survienne
  - aussi, c'est un truc possible, mais pas normal dans cette utilisation là, donc des fois bon, ça chie, DONC ON SPAM

---

➜ J'peux vous aider à le mettre en place, mais **vous le faites uniquement dans un cadre privé, chez vous, ou avec des VMs**

➜ **Je vous conseille 3 machines Linux**, Alice Bob et Eve. La commande `[arping](https://sandilands.info/sgordon/arp-spoofing-on-wired-lan)` pourra vous carry : elle permet d'envoyer manuellement des trames ARP avec le contenu de votre choix.

GLHF.

# III. DHCP you too my brooo

*DHCP* pour *Dynamic Host Configuration Protocol* est notre p'tit pote qui nous file des IPs quand on arrive dans un réseau, parce que c'est chiant de le faire à la main :)

Quand on arrive dans un réseau, notre PC contacte un serveur DHCP, et récupère généralement 3 infos :

- **1.** une IP à utiliser
- **2.** l'adresse IP de la passerelle du réseau
- **3.** l'adresse d'un serveur DNS joignable depuis ce réseau

L'échange DHCP  entre un client et le serveur DHCP consiste en 4 trames : **DORA**, que je vous laisse chercher sur le web vous-mêmes : D

🌞 **Wireshark it**

- identifiez les 4 trames DHCP lors d'un échange DHCP
  - mettez en évidence les adresses source et destination de chaque trame
- identifiez dans ces 4 trames les informations **1**, **2** et **3** dont on a parlé juste au dessus

🦈 **PCAP qui contient l'échange DORA**

> **Soucis** : l'échange DHCP ne se produit qu'à la première connexion. **Pour forcer un échange DHCP**, ça dépend de votre OS. Sur **GNU/Linux**, avec `dhclient` ça se fait bien. Sur **Windows**, le plus simple reste de définir une IP statique pourrie sur la carte réseau, se déconnecter du réseau, remettre en DHCP, se reconnecter au réseau. Sur **MacOS**, je connais peu mais Internet dit qu'c'est po si compliqué, appelez moi si besoin.