**TP 1**

I) Découverte en solo
1. Affichage d'informations sur la pile TCP/IP locale


Nom, adresse MAC et adresse IP de l'interface WiFi : Commande : ```ip a```  
Résultat : 
```
3: wlan0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
    link/ether bc:09:1b:17:26:59 brd ff:ff:ff:ff:ff:ff
    inet 10.33.16.132/22 brd 10.33.19.255 scope global dynamic noprefixroute wlan0
       valid_lft 82263sec preferred_lft 82263sec
    inet6 fe80::ed9:8647:2a12:2c90/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever

```

Utilisez une commande pour connaître l'adresse IP de la passerelle (ou gateway) de votre carte WiFi : Commande : ```ip r```  
Résultat : 
```
default via 10.33.19.254 dev wlan0 proto dhcp src 10.33.16.132 metric 600 
10.33.16.0/22 dev wlan0 proto kernel scope link src 10.33.16.132 metric 600 
```
À l'aide d'une commande, affichez votre table ARP, et déterminez ainsi l'adresse MAC de la passerelle : Commande : ```arp -a```  
Résultat : 
```
_gateway (10.33.19.254) at 00:c0:e7:e0:04:4e [ether] on wlan0
```
Mon adresse MAC est donc : ```00:c0:e7:e0:04:4e```

*Change of OS because my Linux have problems...*

Trouvez l'IP, la MAC et la gateway pour l'interface WiFi de votre PC
![alt img](https://media.discordapp.net/attachments/1016352288702484512/1026486765885857872/unknown.png?width=940&height=662)  

Changez l'adresse IP de votre carte WiFi pour une autre : Verifcation of new IP : 
```
Carte réseau sans fil Wi-Fi :
   Suffixe DNS propre à la connexion. . . :
   Adresse IPv6 de liaison locale. . . . .: fe80::ed22:85a3:64c9:d3b%19
   Adresse IPv4. . . . . . . . . . . . . .: 10.33.16.131
   Masque de sous-réseau. . . . . . . . . : 255.255.252.0
   Passerelle par défaut. . . . . . . . . : 10.33.19.254
```

