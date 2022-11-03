# TP4 : TCP, UDP et services réseau

# Sommaire

- [Sommaire](#sommaire)
- [I. First steps](#i-first-steps)
- [II. Mise en place](#ii-mise-en-place)
  - [1. SSH](#1-ssh)
  - [2. Routage](#2-routage)
- [III. DNS](#iii-dns)
  - [1. Présentation](#1-présentation)
  - [2. Setup](#2-setup)
  - [3. Test](#3-test)

# I. First steps

Apps or services chosen : 
- deezer.com
- Discord
- IP Webcam
- twitch.tv
- youtube.com

🌞 **Déterminez, pour ces 5 applications, si c'est du TCP ou de l'UDP**

| Application | IP | Port | Port local | Protocol |
|-------------|----|------|------------|----------|
| *deezer.com* | `78.40.123.97` | `443` | `47618` | TCP |
| *Discord (visio)* | `35.214.188.78` | `50002` | `44020` | UDP |
| *IP Webcam* | (Ip of the phone)192.168.1.11 | `8080` | `53382` | TCP |
| *twitch.tv* | `52.223.195.231` | `443` | `48918` | TCP |
| *youtube.com* | `2a00:1450:4007:3::6` | `443` | `58550` | QUIC (Quick UDP Internet Connections) |

🌞 **Demandez l'avis à votre OS**

The `ss -tu` command is used to display TCP and UDP connections.
```bash
nathan@nathan-SSD-Linux:~/Documents/Github/tp-b1-reseau$ ss -tu
Netid             State             Recv-Q             Send-Q                                                    Local Address:Port                                                      Peer Address:Port              Process            
udp               ESTAB             0                  0                                                192.168.1.42%wlp0s20f3:bootpc                                                     192.168.1.1:bootps                               
udp               ESTAB             0                  0                                                          192.168.1.42:50553                                                  162.159.134.233:https                                
udp               ESTAB             0                  0                              [2a01:cb19:9f7:5a00:2915:7f3a:af86:76a7]:54461                                       [2a00:1450:4007:819::200e]:https                                
udp               ESTAB             0                  0                              [2a01:cb19:9f7:5a00:2915:7f3a:af86:76a7]:59515                                            [2a00:1450:4007:3::6]:https                                
tcp               ESTAB             0                  0                                                          192.168.1.42:40588                                                   199.232.82.167:https                                
tcp               ESTAB             0                  0                                                          192.168.1.42:58734                                                    44.231.31.141:https                                
tcp               ESTAB             0                  0                                                          192.168.1.42:33568                                                     78.40.123.97:https                                
tcp               ESTAB             0                  0                                                          192.168.1.42:37338                                                    99.181.69.173:https                                
tcp               ESTAB             0                  0                                                          192.168.1.42:55054                                                   199.232.82.167:https                                
tcp               ESTAB             0                  0                                                          192.168.1.42:36314                                                  162.159.134.234:https                                
tcp               ESTAB             0                  0                                                          192.168.1.42:40614                                                   199.232.82.167:https                                
tcp               ESTAB             0                  0                                                          192.168.1.42:35834                                                  162.159.129.235:https                                
tcp               ESTAB             0                  0                                                          192.168.1.42:38300                                                     23.55.48.172:https                                
tcp               ESTAB             0                  0                                                          192.168.1.42:46354                                                       23.160.0.0:https                                
tcp               ESTAB             0                  0                                                          192.168.1.42:56784                                                    90.50.223.150:https                                
tcp               ESTAB             0                  0                                                          192.168.1.42:42786                                                    20.250.85.194:https                                
tcp               ESTAB             0                  0                                                          192.168.1.42:42604                                                  151.101.121.208:https                                
tcp               ESTAB             0                  0                                                          192.168.1.42:45452                                                     52.223.198.2:https                                
tcp               ESTAB             0                  0                                                          192.168.1.42:35546                                                   185.159.104.91:https                                
tcp               ESTAB             0                  0                                                          192.168.1.42:39508                                                   192.229.133.15:https                                
tcp               ESTAB             0                  0                                                          192.168.1.42:35202                                                   199.232.82.214:https                                
tcp               ESTAB             0                  0                                                          192.168.1.42:47748                                                     13.249.9.109:https                                
tcp               ESTAB             0                  0                                                          192.168.1.42:40572                                                   199.232.82.167:https                                
tcp               ESTAB             0                  0                                                          192.168.1.42:55070                                                   199.232.82.167:https                                
tcp               ESTAB             0                  0                                                          192.168.1.42:34386                                                  185.199.111.154:https                                
tcp               ESTAB             0                  0                                                          192.168.1.42:38332                                                    54.203.18.141:https                                
tcp               ESTAB             0                  0                                                          192.168.1.42:54348                                                   44.232.154.146:https                                
tcp               ESTAB             0                  0                                                          192.168.1.42:43172                                                    52.222.158.53:https                                
tcp               ESTAB             0                  0                                                          192.168.1.42:37104                                                   199.232.82.214:https                                
tcp               ESTAB             0                  0                                                          192.168.1.42:36364                                                     54.239.22.51:https                                
tcp               ESTAB             0                  0                                                          192.168.1.42:56412                                                    52.84.174.120:https                                
tcp               ESTAB             0                  0                                                          192.168.1.42:43500                                                    92.122.188.40:https                                
tcp               ESTAB             0                  0                                                          192.168.1.42:40602                                                   199.232.82.167:https                                
tcp               ESTAB             0                  0                                                          192.168.1.42:57938                                                     54.70.91.172:https                                
tcp               ESTAB             0                  0                                                          192.168.1.42:39496                                                   192.229.133.15:https                                
tcp               ESTAB             0                  0                              [2a01:cb19:9f7:5a00:2915:7f3a:af86:76a7]:59074                             [2600:9000:218c:cc00:c:132:48e:f021]:https                                
tcp               ESTAB             0                  0                              [2a01:cb19:9f7:5a00:2915:7f3a:af86:76a7]:46298                                           [2606:4700::6811:83ab]:https                                
tcp               ESTAB             0                  0                                                 [::ffff:192.168.1.42]:47664                                            [::ffff:192.168.1.11]:1716                                 
tcp               ESTAB             0                  0                              [2a01:cb19:9f7:5a00:2915:7f3a:af86:76a7]:41296                           [2600:9000:20e1:9000:2:5db4:1800:93a1]:https                                
tcp               ESTAB             0                  0                              [2a01:cb19:9f7:5a00:2915:7f3a:af86:76a7]:60126                           [2600:9000:2171:9600:2:8588:ed80:93a1]:https                                
tcp               ESTAB             0                  0                              [2a01:cb19:9f7:5a00:2915:7f3a:af86:76a7]:56140                                           [2606:4700::6811:d3cc]:https                                
tcp               ESTAB             0                  0                              [2a01:cb19:9f7:5a00:2915:7f3a:af86:76a7]:35900                          [2600:9000:2450:e000:19:f28c:cd8e:cd41]:https                                
tcp               ESTAB             0                  0                                                 [::ffff:192.168.1.42]:43204                                            [::ffff:192.168.1.12]:1716                                 
```
The deezer pcapng file can be found [here](./assets/deezer.pcapng)  
The discord pcapng file can be found [here](./assets/discord.pcapng)  
The Ipcam pcapng file can be found [here](./assets/ipcam.pcapng)  
The Twitch pcapng file can be found [here](./assets/twitch.pcapng)  
The Youtube pcapng file can be found [here](./assets/youtube.pcapng)  

# II. Mise en place

## 1. SSH

🖥️ **Machine `node1.tp4.b1`**

🌞 **Examinez le trafic dans Wireshark**

SSH uses normal TCP because it is a connection to a user on another network.

- [Three way handshake](./assets/3WayHandshake.pcapng)  
- [SSH Traffic](./assets/SSHTraffic.pcapng)  
- [End SSH](./assets/EndSSH.pcapng)

🌞 **Demandez aux OS**

On my machine, I use the command ss to see ssh connections.
```bash
tcp      ESTAB     0          0                                10.4.1.1:50364              10.4.1.11:ssh
```


```bash
tcp      ESTAB     0          0                                10.4.1.11:ssh               10.4.1.1:50364
```
# III. DNS

🌞 **Dans le rendu, je veux**

```bash
[wiibleyde@dns-server ~]$ sudo cat /etc/named.conf

options {
        listen-on port 53 { 127.0.0.1; };
        listen-on-v6 port 53 { ::1; };
        directory       "/var/named";
        dump-file       "/va
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

zone "." IN {
        type hint;
        file "named.ca";
};

zone "tp4.b1" IN {
        type master;
        file "tp4.b1.db";
        allow-update { none; };
        allow-query { any; };
};

zone "1.4.10.in-addr.arpa" IN {
        type master;
        file "tp4.b1.rev";
        allow-update { none; };
        allow-query { any; };
};
```
```bash
[wiibleyde@dns-server ~]$ sudo cat /var/named/tp4.b1.rev

$TTL 86400
@ IN SOA dns-server.tp4.b1. admin.tp4.b1. (
        2019061800 ;Serial
        3600 ;Refresh
        1800 ;Retry
        604800 ;Expire
        86400 ;Minimum TTL
)

; Infos sur le server DNS lui même (NS =NameServer)
@ IN NS dns-server.tp4.b1.

; Reverse lookup for Name Server

201 IN PTR dns-server.tp4.b1.

11 IN PTR node1.tp4.b1
```
```bash
[wiibleyde@dns-server ~]$ sudo cat /var/named/tp4.b1.db

$TTL 86400
@ IN SOA dns-server.tp4.b1 admin.tp4.b1 (
        2019061800 ;Serial
        3600 ;Refresh
        1800 ;Retry
        604800 ;Expire
        86400 ;Minimum TTL
)

; Infos sur le DNS lui même (NS = NameServer)
@ IN NS dns-server.tp4.b1.

; Enregistrements DNS pour faire correspondre des noms à des IPs

dns-server IN A 10.4.1.201

node1 IN A 10.4.1.11
```
```bash
sudo systemctl status named

 Active: active (running) since Thu 2022-11-03 15:54:40 CEST; 37min ago
```
```bash
ss -t -l -n

LISTEN               0                    10                                     127.0.0.1:53                                     0.0.0.0:*
```

🌞 **Ouvrez le bon port dans le firewall**

```bash
[wiibleyde@dns-server ~]$ sudo firewall-cmd --add-port=53/tcp --permanent
[sudo] password for wiibleyde:
success
[wiibleyde@dns-server ~]$ sudo firewall-cmd --reload
success
```

## 3. Test

🌞 **Sur la machine `node1.tp4.b1`**

```bash
[wiibleyde@node1 ~]$ dig node1.tp4.b1

;; ANSWER SECTION:
node1.tp4.b1.           86400   IN      A       10.4.1.11

;; SERVER: 10.4.1.201#53(10.4.1.201)
```
```bash
[wiibleyde@node1 ~]$ dig dns-server.tp4.b1

;; ANSWER SECTION:
dns-server.tp4.b1.      86400   IN      A       10.4.1.201

;; SERVER: 10.4.1.201#53(10.4.1.201)
```
```bash
[wiibleyde@node1 ~]$ dig www.google.com

;; ANSWER SECTION:
www.google.com.         300     IN      A       142.250.179.100

;; SERVER: 10.4.1.201#53(10.4.1.201)
```

🌞 **Sur votre PC**

I had to go on Windows to do this part because for an obscure reason, I can't ping `node1.tp4.b1` from my Linux machine... I have certainly missed something but I don't know what.
```
PS C:\Users\natha> nslookup
Serveur par défaut :   dns.google
Address:  8.8.8.8

> set type=soa
> node1.tp4.b1 10.4.1.201
```
The DNS request pcapng file is available [here](./assets/DNS%20request.pcapng).
