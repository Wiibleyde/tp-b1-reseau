# TP3 : On va router des trucs

## Sommaire

- [TP3 : On va router des trucs](#tp3--on-va-router-des-trucs)
  - [Sommaire](#sommaire)
  - [I. ARP](#i-arp)
    - [1. Echange ARP](#1-echange-arp)
    - [2. Analyse de trames](#2-analyse-de-trames)
  - [II. Routage](#ii-routage)
    - [1. Mise en place du routage](#1-mise-en-place-du-routage)
    - [2. Analyse de trames](#2-analyse-de-trames-1)
    - [3. Accès internet](#3-accès-internet)
  - [III. DHCP](#iii-dhcp)
    - [1. Mise en place du serveur DHCP](#1-mise-en-place-du-serveur-dhcp)
    - [2. Analyse de trames](#2-analyse-de-trames-2)

## I. ARP

Première partie simple, on va avoir besoin de 2 VMs.

| Machine  | `10.3.1.0/24` |
|----------|---------------|
| `john`   | `10.3.1.11`   |
| `marcel` | `10.3.1.12`   |

```schema
   john               marcel
  ┌─────┐             ┌─────┐
  │     │    ┌───┐    │     │
  │     ├────┤ho1├────┤     │
  └─────┘    └───┘    └─────┘
```

### 1. Echange ARP

🌞**Générer des requêtes ARP**

Ping John --> Marcel : 
```
[root@localhost ~]# ping 10.3.1.12
PING 10.3.1.12 (10.3.1.12) 56(84) bytes of data.
64 bytes from 10.3.1.12: icmp_seq=1 ttl=64 time=0.542 ms
64 bytes from 10.3.1.12: icmp_seq=2 ttl=64 time=0.538 ms
64 bytes from 10.3.1.12: icmp_seq=3 ttl=64 time=0.607 ms
64 bytes from 10.3.1.12: icmp_seq=4 ttl=64 time=0.397 ms
^C
--- 10.3.1.12 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3063ms
rtt min/avg/max/mdev = 0.397/0.521/0.607/0.076 ms
```
Ping Marcel --> John : 
```
[root@localhost ~]# ping 10.3.1.11
PING 10.3.1.11 (10.3.1.11) 56(84) bytes of data.
64 bytes from 10.3.1.11: icmp_seq=1 ttl=64 time=0.365 ms
64 bytes from 10.3.1.11: icmp_seq=2 ttl=64 time=0.465 ms
64 bytes from 10.3.1.11: icmp_seq=3 ttl=64 time=0.341 ms
64 bytes from 10.3.1.11: icmp_seq=4 ttl=64 time=0.545 ms
^C
--- 10.3.1.11 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3048ms
rtt min/avg/max/mdev = 0.341/0.429/0.545/0.081 ms
```

John ARP : 
```bash
root@localhost ~# ip neigh show
10.3.1.1 dev enp0s8 lladdr 0a:00:27:00:00:00 STALE
10.3.1.12 dev enp0s8 lladdr 08:00:27:b7:66:a3 STALE
```
Marcel ARP : 
```bash
[root@localhost ~]# ip neigh show
10.3.1.11 dev enp0s8 lladdr 08:00:27:cf:c0:e7 STALE
10.3.1.1 dev enp0s8 lladdr 0a:00:27:00:00:00 REACHABLE
```
Marcel in John ARP's table : `10.3.1.11 dev enp0s8 lladdr 08:00:27:cf:c0:e7 STALE`  
John in Marcel ARP's table : `10.3.1.12 dev enp0s8 lladdr 08:00:27:b7:66:a3 STALE`  

In the `ip a` command, we can find the MAC adress : 
- John : `08:00:27:cf:c0:e7` (same as the Marcel ARP table)
- Marcel : `08:00:27:b7:66:a3` (same as the John ARP table)

### 2. Analyse de trames

🌞**Analyse de trames**

The Wireshark file can be found **[here](./assets/tp2_arp.pcapng)**.

## II. Routage

Vous aurez besoin de 3 VMs pour cette partie. **Réutilisez les deux VMs précédentes.**

| Machine  | `10.3.1.0/24` | `10.3.2.0/24` |
|----------|---------------|---------------|
| `router` | `10.3.1.254`  | `10.3.2.254`  |
| `john`   | `10.3.1.11`   | no            |
| `marcel` | no            | `10.3.2.12`   |

> Je les appelés `marcel` et `john` PASKON EN A MAR des noms nuls en réseau 🌻

```schema
   john                router              marcel
  ┌─────┐             ┌─────┐             ┌─────┐
  │     │    ┌───┐    │     │    ┌───┐    │     │
  │     ├────┤ho1├────┤     ├────┤ho2├────┤     │
  └─────┘    └───┘    └─────┘    └───┘    └─────┘
```

### 1. Mise en place du routage

🌞**Activer le routage sur le noeud `router`**

```bash
sudo firewall-cmd --add-masquerade --zone=public --permanent
```

🌞**Ajouter les routes statiques nécessaires pour que `john` et `marcel` puissent se `ping`**

- il faut ajouter une seule route des deux côtés
- une fois les routes en place, vérifiez avec un `ping` que les deux machines peuvent se joindre

### 2. Analyse de trames

🌞**Analyse des échanges ARP**

- ARP and ICMP for PC1 in [this file](./assets/scan_pc1.pcap)  
- ARP and ICMP for PC1 in [this file](./assets/scan_routeur.pcap)

| ordre | type trame | IP source | MAC source | IP destination | MAC destination |
|-------|-------------|-----------|-------------------------|----------------|----------------------------|
| 1 | Requête ARP | `10.3.1.11` | `john` `08:00:27:cf:c0:e7` | `10.3.1.254` | `Broadcast` `FF:FF:FF:FF:FF` |
| 2 | Réponse ARP | `10.3.1.254` | `Routeur` `08:00:27:6e:27:e9` | `10.3.1.11` | `john` `08:00:27:cf:c0:e7` |
| 3 | Ping | `10.3.1.11`| `john` `08:00:27:cf:c0:e7`| `10.3.2.12` | `Routeur` `08:00:27:6e:27:e9` |
| 4 | Requête ARP | `10.3.2.254` | `routeur` `08:00:27:1a:c1:f4` | `10.3.2.12` | `Broadcast` `ff:ff:ff:ff:ff` |
| 5 | Réponse ARP | `10.3.2.12` | `marcel` `08:00:27:6e:27:e9` | `10.3.2.254` | `routeur` `08:00:27:1a:c1:f4` |
| 6 | Ping | `10.3.2.254`| `routeur` `08:00:27:1a:c1:f4`| `10.3.2.12` | `Marcel` `08:00:27:6e:27:e9` |
| 7 | Pong | `10.3.2.12` | `Marcel` `08:00:27:6e:27:e9` | `10.3.2.254` | `routeur` `08:00:27:1a:c1:f4` |
| 8 | Pong | `10.3.2.12` | `Routeur` `08:00:27:6e:27:e9` | `10.3.1.11` | `john` `08:00:27:cf:c0:e7` |

### 3. Accès internet

🌞**Donnez un accès internet à vos machines**

- Ping of john at 8.8.8.8 : 
```bash
[root@localhost ~]# ping 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=61 time=23.4 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=61 time=23.0 ms
64 bytes from 8.8.8.8: icmp_seq=3 ttl=61 time=24.6 ms
64 bytes from 8.8.8.8: icmp_seq=4 ttl=61 time=24.1 ms
64 bytes from 8.8.8.8: icmp_seq=5 ttl=61 time=23.9 ms
^C
--- 8.8.8.8 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4002ms
rtt min/avg/max/mdev = 22.968/23.797/24.643/0.571 ms
```

- Dig test 
```bash
[root@localhost ~]# dig gitlab.com

; <<>> DiG 9.16.23-RH <<>> gitlab.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 51813
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
;; QUESTION SECTION:
;gitlab.com.                    IN      A

;; ANSWER SECTION:
gitlab.com.             273     IN      A       172.65.251.78

;; Query time: 24 msec
;; SERVER: 1.1.1.1#53(1.1.1.1)
;; WHEN: Thu Oct 13 14:21:52 CEST 2022
;; MSG SIZE  rcvd: 55
```

🌞**Analyse de trames**

The wireshark file can be found **[here](./assets/tp3_routage_internet.pcapng)**.

| ordre | type trame | IP source | MAC source | IP destination | MAC destination |
|-------|------------|--------------------|-------------------------|----------------|-----------------|
| 1 | ping | `john` `10.3.1.12` | `john` `08:00:27:cf:c0:e7` | `8.8.8.8` | `08:00:27:6e:27:e9`|
| 2 | pong | `8.8.8.8` | `08:00:27:6e:27:e9` | `10.3.1.11` | `08:00:27:cf:c0:e7` |

## III. DHCP

On reprend la config précédente, et on ajoutera à la fin de cette partie une 4ème machine pour effectuer des tests.

| Machine | `10.3.1.0/24` | `10.3.2.0/24` |
|----------|----------------------------|---------------|
| `router` | `10.3.1.254` | `10.3.2.254` |
| `john` | `10.3.1.11` | no |
| `bob`    | oui mais pas d'IP statique | no |
| `marcel` | no | `10.3.2.12` |

```schema
   john               router              marcel
  ┌─────┐             ┌─────┐             ┌─────┐
  │     │    ┌───┐    │     │    ┌───┐    │     │
  │     ├────┤ho1├────┤     ├────┤ho2├────┤     │
  └─────┘    └─┬─┘    └─────┘    └───┘    └─────┘
   john        │
  ┌─────┐      │
  │     │      │
  │     ├──────┘
  └─────┘
```

### 1. Mise en place du serveur DHCP

🌞**Sur la machine `john`, vous installerez et configurerez un serveur DHCP** (go Google "rocky linux dhcp server").

After creating Bob machine I can see in the command `systemctl status dhcpd` that the DHCP server is running, because it gave an IP address to Bob.
```bash
Oct 13 14:58:39 localhost.localdomain dhcpd[791]: DHCPOFFER on 10.3.1.3 to 08:00:27:31:c7:c7 via enp0s8
Oct 13 14:58:39 localhost.localdomain dhcpd[791]: DHCPREQUEST for 10.3.1.3 (10.3.1.11) from 08:00:27:31:c7:c7 via e>
Oct 13 14:58:39 localhost.localdomain dhcpd[791]: DHCPACK on 10.3.1.3 to 08:00:27:31:c7:c7 via enp0s8
```

🌞**Améliorer la configuration du DHCP**

Marcel `ip a` command output :
```bash
2: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:31:c7:c7 brd ff:ff:ff:ff:ff:ff
    inet 10.3.1.3/24 brd 10.3.1.255 scope global dynamic noprefixroute enp0s8
       valid_lft 652sec preferred_lft 652sec
    inet 10.3.1.4/24 brd 10.3.1.255 scope global secondary dynamic enp0s8
       valid_lft 737sec preferred_lft 737sec
    inet6 fe80::e63d:c143:2e6a:398e/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
```

Marcel `ping 1.1.1.1` command output :

```bash
[root@localhost ~]# ping 1.1.1.1
PING 1.1.1.1 (1.1.1.1) 56(84) bytes of data.
64 bytes from 1.1.1.1: icmp_seq=1 ttl=61 time=21.5 ms
64 bytes from 1.1.1.1: icmp_seq=2 ttl=61 time=23.0 ms
64 bytes from 1.1.1.1: icmp_seq=3 ttl=61 time=24.1 ms
64 bytes from 1.1.1.1: icmp_seq=4 ttl=61 time=117 ms
^C
--- 1.1.1.1 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3004ms
rtt min/avg/max/mdev = 21.466/46.276/116.543/40.579 ms
```

Marcel `dig google.com` command output :

```bash
[root@localhost ~]# dig google.com

; <<>> DiG 9.16.23-RH <<>> google.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 24950
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
;; QUESTION SECTION:
;google.com.                    IN      A

;; ANSWER SECTION:
google.com.             300     IN      A       216.58.206.238

;; Query time: 23 msec
;; SERVER: 1.1.1.1#53(1.1.1.1)
;; WHEN: Thu Oct 13 15:33:19 CEST 2022
;; MSG SIZE  rcvd: 55
```

### 2. Analyse de trames

🌞**Analyse de trames**

The wireshark file can be found **[here](./assets/tp3_dhcp.pcapng)**.

