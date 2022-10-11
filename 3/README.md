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
```
root@localhost ~]# ip neigh show
10.3.1.1 dev enp0s8 lladdr 0a:00:27:00:00:00 STALE
10.3.1.12 dev enp0s8 lladdr 08:00:27:b7:66:a3 STALE
```
Marcel ARP : 
```
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

```
sudo firewall-cmd --add-masquerade --zone=public --permanent
```

🌞**Ajouter les routes statiques nécessaires pour que `john` et `marcel` puissent se `ping`**

- il faut ajouter une seule route des deux côtés
- une fois les routes en place, vérifiez avec un `ping` que les deux machines peuvent se joindre

![THE SIZE](./pics/thesize.png)

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
```
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

🌞**Analyse de trames**

- effectuez un `ping 8.8.8.8` depuis `john`
- capturez le ping depuis `john` avec `tcpdump`
- analysez un ping aller et le retour qui correspond et mettez dans un tableau :

| ordre | type trame | IP source          | MAC source              | IP destination | MAC destination |     |
|-------|------------|--------------------|-------------------------|----------------|-----------------|-----|
| 1     | ping       | `john` `10.3.1.12` | `john` `AA:BB:CC:DD:EE` | `8.8.8.8`      | ?               |     |
| 2     | pong       | ...                | ...                     | ...            | ...             | ... |

🦈 **Capture réseau `tp2_routage_internet.pcapng`**

## III. DHCP

On reprend la config précédente, et on ajoutera à la fin de cette partie une 4ème machine pour effectuer des tests.

| Machine  | `10.3.1.0/24`              | `10.3.2.0/24` |
|----------|----------------------------|---------------|
| `router` | `10.3.1.254`               | `10.3.2.254`  |
| `john`   | `10.3.1.11`                | no            |
| `bob`    | oui mais pas d'IP statique | no            |
| `marcel` | no                         | `10.3.2.12`   |

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

- installation du serveur sur `john`
- créer une machine `bob`
- faites lui récupérer une IP en DHCP à l'aide de votre serveur

> Il est possible d'utilise la commande `dhclient` pour forcer à la main, depuis la ligne de commande, la demande d'une IP en DHCP, ou renouveler complètement l'échange DHCP (voir `dhclient -h` puis call me et/ou Google si besoin d'aide).

🌞**Améliorer la configuration du DHCP**

- ajoutez de la configuration à votre DHCP pour qu'il donne aux clients, en plus de leur IP :
  - une route par défaut
  - un serveur DNS à utiliser
- récupérez de nouveau une IP en DHCP sur `bob` pour tester :
  - `marcel` doit avoir une IP
    - vérifier avec une commande qu'il a récupéré son IP
    - vérifier qu'il peut `ping` sa passerelle
  - il doit avoir une route par défaut
    - vérifier la présence de la route avec une commande
    - vérifier que la route fonctionne avec un `ping` vers une IP
  - il doit connaître l'adresse d'un serveur DNS pour avoir de la résolution de noms
    - vérifier avec la commande `dig` que ça fonctionne
    - vérifier un `ping` vers un nom de domaine

### 2. Analyse de trames

🌞**Analyse de trames**

- lancer une capture à l'aide de `tcpdump` afin de capturer un échange DHCP
- demander une nouvelle IP afin de générer un échange DHCP
- exportez le fichier `.pcapng`

🦈 **Capture réseau `tp2_dhcp.pcapng`**

