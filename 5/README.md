# MITM et DNS Spoofing

## Pre-requisites

* 3 virtuals machines :
    * 1 for the attacker
    * 1 for the victim
    * 1 for the router
* 2 interfaces on the router
    * 1 for the internet
    * 1 for the local network
    * Configured to route traffic from network to internet
* 1 interface on the victim (host only, not NAT)
* 1 interface on the attacker (host only, not NAT)
    * You also need to modify rules on the attacker firewall to drop all packets comming for 53 UDP port in order to block response from official DNS server.
    ```bash
    sudo /usr/sbin/iptables -A FORWARD -p udp --dport 53 -j DROP
    ```

(If you want to access to a working version of VMs, contact me)

## Launching the attack

The code of the python script can be found in the this [repository](https://github.com/Wiibleyde/MITM). 

To launch the attack, you need to run the following command:
```bash
sudo python3 main.py
```

After starting the program, you will be asked to select the IP address of the victim and the IP address of the router.

And that's it, the attack is launched.
If the victim try to access to "google.com", the program will redirect him to the IP address of the attacker. If he ask any other domain, the domain resoluton is correctly done.

## Stopping the attack

To stop the attack, you have to kill the python process.

## How it works

The program is composed of 2 parts:
1) First part : MITM
    - The first part is a network scanner that scans the network to find all the IP address and MAC address available on the network and display them.
    - Then you select the IP address of the victim and the IP address of the router.
    - The program will then launch a ARP poisoning attack on the victim and the router by sending ARP response with the MAC address of the attacker.
    - You can find a pcap file [here](./assets/arp_poisoning.pcap), that show the arp poisoning attack.
2) Second part : DNS Spoofing
    - This second part intercept all the DNS request from the victim, if request is for "google.com", the program will send a DNS response with the IP address of the attacker. Either the program will send a DNS response with the correct IP address of the domain by resolving it with google DNS server (`8.8.8.8`). 
    - To show you, dig command on the victim will show you the IP address of the attacker :
    ```
    $ dig google.com
    ; <<>> DiG 9.16.33-Debian <<>> google.com
    ;; global options: +cmd
    ;; Got answer:
    ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 62051
    ;; flags: qr rd; QUERY: 1, ANSWER: 2, AUTHORITY: 0, ADDITIONAL: 0
    ;; WARNING: recursion requested but not available

    ;; QUESTION SECTION:
    ;google.com.                    IN      A

    ;; ANSWER SECTION:
    google.com.             10      IN      A       192.168.60.12
    google.com.             10      IN      A       192.168.60.12

    ;; Query time: 3 msec
    ;; SERVER: 10.0.2.3#53(10.0.2.3)
    ;; WHEN: Sun Nov 13 15:50:30 CET 2022
    ;; MSG SIZE  rcvd: 80
    ```
    ```
    $ dig ynov.com

    ; <<>> DiG 9.16.33-Debian <<>> ynov.com
    ;; global options: +cmd
    ;; Got answer:
    ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 44788
    ;; flags: qr rd ra; QUERY: 1, ANSWER: 3, AUTHORITY: 0, ADDITIONAL: 0

    ;; QUESTION SECTION:
    ;ynov.com.                      IN      A

    ;; ANSWER SECTION:
    ynov.com.               300     IN      A       104.26.11.233
    ynov.com.               300     IN      A       104.26.10.233
    ynov.com.               300     IN      A       172.67.74.226

    ;; Query time: 87 msec
    ;; SERVER: 10.0.2.3#53(10.0.2.3)
    ;; WHEN: Sun Nov 13 18:22:35 CET 2022
    ;; MSG SIZE  rcvd: 74
    ```
## The python script

### Libraries

The python script needs the following libraries:
- [Scapy](https://scapy.net/) : Used to send and receive packets. 
- [ipaddress](https://docs.python.org/3/library/ipaddress.html) : Used to check if the IP address entered by the user is valid and also get the IP address of the attacker. It's also used to manipulate IP addresses.
- [socket](https://docs.python.org/3/library/socket.html) : Used to get the network mask.
- [fcntl](https://docs.python.org/3/library/fcntl.html) : Used to get the network mask.
- [struct](https://docs.python.org/3/library/struct.html) : Used to get the network mask.
- [os](https://docs.python.org/3/library/os.html) : Used to get the name of the machine to differentiate the developper machine and the attacker VM to select the correct interface.

### Functions

The python script uses the following functions:
- `interfaceChecker()` : Check the name of the computer and return the interface name (it depend of the machine).
- `getNetworkMask()` : Get the network mask of the network.
- `makeARPRequest()` : Sends an ARP requests to the network and returns the answered and unanswered packets.
- `forwardDnsSpoofing()` : Call `forwardDNS()` function when a DNS request is received.
    - `forwardDNS()` : Either send a DNS response with the IP address of the attacker or send a DNS response with the correct IP address of the domain by resolving it with google DNS server.
- `main()` : Main function of the program.