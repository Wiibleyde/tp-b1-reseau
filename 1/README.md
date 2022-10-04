**TP 1**

I) Découverte en solo
1. Affichage d'informations sur la pile TCP/IP locale


Nom, adresse MAC et adresse IP de l'interface WiFi : Commande : ```ip a```  
Result: 
```
3: wlan0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
    link/ether bc:09:1b:17:26:59 brd ff:ff:ff:ff:ff:ff
    inet 10.33.16.132/22 brd 10.33.19.255 scope global dynamic noprefixroute wlan0
       valid_lft 82263sec preferred_lft 82263sec
    inet6 fe80::ed9:8647:2a12:2c90/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever

```

Utilisez une commande pour connaître l'adresse IP de la passerelle (ou gateway) de votre carte WiFi : Commande : ```ip r```  
Result: 
```
default via 10.33.19.254 dev wlan0 proto dhcp src 10.33.16.132 metric 600 
10.33.16.0/22 dev wlan0 proto kernel scope link src 10.33.16.132 metric 600 
```
À l'aide d'une commande, affichez votre table ARP, et déterminez ainsi l'adresse MAC de la passerelle : Commande : ```arp -a```  
Result: 
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

It's possible to lose Internet during this operation because, we could be on the same IP of another device.  

Choisissez une IP qui commence par "10.10.10."  
Result : 
```
Carte Ethernet Ethernet 3 :

   Suffixe DNS propre à la connexion. . . :
   Adresse IPv6 de liaison locale. . . . .: fe80::bc56:7749:4fd1:66a8%22
   Adresse IPv4. . . . . . . . . . . . . .: 10.10.10.11
   Masque de sous-réseau. . . . . . . . . : 255.255.255.0
   Passerelle par défaut. . . . . . . . . : 10.10.10.255
```

Vérifier que les deux machines se joignent :  
Result : 
```
Envoi d’une requête 'Ping'  10.10.10.12 avec 32 octets de données :
Réponse de 10.10.10.12 : octets=32 temps=1 ms TTL=128
Réponse de 10.10.10.12 : octets=32 temps=1 ms TTL=128
Réponse de 10.10.10.12 : octets=32 temps=1 ms TTL=128
Réponse de 10.10.10.12 : octets=32 temps=1 ms TTL=128

Statistiques Ping pour 10.10.10.12:
    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 1ms, Maximum = 1ms, Moyenne = 1ms
```

Déterminer l'adresse MAC de votre correspondant :  
Result :  
```
Interface : 10.10.10.11 --- 0x16
  Adresse Internet      Adresse physique      Type
  10.10.10.12           50-eb-f6-c4-51-a8     dynamique
```

Essayez de ping l'adresse IP 1.1.1.1, c'est un serveur connu de CloudFlare :  
Result :  
```
Envoi d’une requête 'Ping'  1.1.1.1 avec 32 octets de données :
Réponse de 1.1.1.1 : octets=32 temps=25 ms TTL=54
Réponse de 1.1.1.1 : octets=32 temps=22 ms TTL=54
Réponse de 1.1.1.1 : octets=32 temps=24 ms TTL=54
Réponse de 1.1.1.1 : octets=32 temps=21 ms TTL=54

Statistiques Ping pour 1.1.1.1:
    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 21ms, Maximum = 25ms, Moyenne = 23ms
```

Result of command : ```tracert -4 1.1.1.1```
```
Détermination de l’itinéraire vers one.one.one.one [1.1.1.1]
avec un maximum de 30 sauts :

  1     1 ms    <1 ms    <1 ms  Gwuill [192.168.137.1] 
  2     *        *        *     Délai d’attente de la demande dépassé.
  3     4 ms    43 ms     3 ms  10.33.19.254 
  4     9 ms     6 ms     5 ms  137.149.196.77.rev.sfr.net [77.196.149.137] 
  5    20 ms    15 ms    15 ms  108.97.30.212.rev.sfr.net [212.30.97.108] 
  6    46 ms    32 ms    34 ms  222.172.136.77.rev.sfr.net [77.136.172.222] 
  7    32 ms    41 ms    34 ms  221.172.136.77.rev.sfr.net [77.136.172.221] 
  8    28 ms    30 ms    32 ms  221.10.136.77.rev.sfr.net [77.136.10.221] 
  9    21 ms    21 ms    22 ms  221.10.136.77.rev.sfr.net [77.136.10.221] 
 10    30 ms    44 ms    25 ms  141.101.67.254 
 11    36 ms    31 ms    31 ms  172.71.128.2 
 12    22 ms    21 ms    21 ms  one.one.one.one [1.1.1.1] 

Itinéraire déterminé.
```

Test of sending messages :  
Result : 
- Client side : 
```
C:\Users\natha\Desktop\netcat-win32-1.12>nc.exe 192.168.137.1 8888
ça marche
Yes !
oui
```
- Server side : 
```
C:\Users\guill\Desktop\netcat-1.11>nc.exe -l -p 8888
ça marche
oui
Yes !
```

Ouvrez un deuxième terminal pendant une session netcat, et utilisez la commande correspondant à votre OS pour repérer la connexion netcat :  
Result :  
- Client side : 
```
 [nc.exe]
  TCP    192.168.137.11:63663   13.69.68.64:443        ESTABLISHED
```

- Server side : 
```
[nc.exe]
  TCP    192.168.137.1:51396    0.0.0.0:0              LISTENING
```

Modifying firewall :  
![alt-img](https://media.discordapp.net/attachments/1021417758778462248/1026832526989344828/unknown.png)
- My server authorize me to ping him with firewall :  
```
C:\Users\natha>ping 192.168.137.1

Envoi d’une requête 'Ping'  192.168.137.1 avec 32 octets de données :
Réponse de 192.168.137.1 : octets=32 temps=1 ms TTL=128
Réponse de 192.168.137.1 : octets=32 temps=1 ms TTL=128
Réponse de 192.168.137.1 : octets=32 temps=2 ms TTL=128
Réponse de 192.168.137.1 : octets=32 temps=1 ms TTL=128

Statistiques Ping pour 192.168.137.1:
    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 1ms, Maximum = 2ms, Moyenne = 1ms
```

- I authorized my server to ping me (with the firewall) : 
```
PS C:\Users\guill> ping 192.168.137.11

Envoi d’une requête 'Ping'  192.168.137.11 avec 32 octets de données :
Réponse de 192.168.137.11 : octets=32 temps=1 ms TTL=128
Réponse de 192.168.137.11 : octets=32 temps=2 ms TTL=128
Réponse de 192.168.137.11 : octets=32 temps=1 ms TTL=128
Réponse de 192.168.137.11 : octets=32 temps=2 ms TTL=128

Statistiques Ping pour 192.168.137.11:
    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 1ms, Maximum = 2ms, Moyenne = 1ms
```

- My DHCP server can be found on IP : 
```
C:\Users\natha>ipconfig /all | findstr /C:"Serveur DHCP"
   Serveur DHCP . . . . . . . . . . . . . : 10.33.19.254
```

- My PC knows : 
```
C:\Users\natha>ipconfig /all | findstr /C:"Serveurs DNS"
   Serveurs DNS. . .  . . . . . . . . . . : 8.8.8.8
```

- The `nslookup` command on `ynov.com` gave this result :
```
C:\Users\natha>nslookup ynov.com
Serveur :   dns.google
Address:  8.8.8.8

Réponse ne faisant pas autorité :
Nom :    ynov.com
Addresses:  2606:4700:20::681a:be9
          2606:4700:20::681a:ae9
          2606:4700:20::ac43:4ae2
          104.26.11.233
          172.67.74.226
          104.26.10.233
```

- The IP  `78.73.21.21` is named `telia.com`
```
C:\Users\natha>nslookup 78.73.21.21
Serveur :   dns.google
Address:  8.8.8.8

Nom :    78-73-21-21-no168.tbcn.telia.com
Address:  78.73.21.21
```

- The IP  `22.146.54.58` don't "exist"
```
C:\Users\natha\Documents\GitHub\tp-b1-reseau> nslookup 22.146.54.58
Serveur :   dns.google
Address:  8.8.8.8

*** dns.google ne parvient pas à trouver 22.146.54.58 : Non-existent domain
```

**Utilisation of WireShark**

When I ping the user which is using WireShark : 
![alt-img](https://media.discordapp.net/attachments/1021417758778462248/1026839751682179122/unknown.png)

When I use NC, the WireShark user see my message (in WireShark) :
![alt-img](https://media.discordapp.net/attachments/1021417758778462248/1026841559695626280/unknown.png?width=1170&height=663)

Une requête DNS. Identifiez dans la capture le serveur DNS à qui vous posez la question : 

![alt-img](https://media.discordapp.net/attachments/1021417758778462248/1026844736788635758/unknown.png)

The server `google.com` has 148.250.74.228 as IP

- When I look a video on Youtube it goes on IP : `74.125.193.93`
