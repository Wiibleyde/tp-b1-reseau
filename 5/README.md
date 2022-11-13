# MITM et DNS Spoofing

## Prerequis

* 3 virtuals machines :
    * 1 for the attacker
    * 1 for the victim
    * 1 for the router
* 2 interfaces on the router
    * 1 for the internet
    * 1 for the local network
* 1 interface on the victim
* 1 interface on the attacker
* You also need to modify rules on the attacker firewall to drop all packets except the ones you want to intercept : (this command drop all the packet comming from port 53)
```bash
sudo /usr/sbin/iptables -A FORWARD -p udp --dport 53 -j DROP
```
(If you want to access to a working version of VMs, you can have them [here](https://auvencecom-my.sharepoint.com/:u:/g/personal/nathan_bonnell_ynov_com/EUpvWaWJm59LtZi3gsgMrKwBqIsvZISuL6sWRReV6mMSCw?e=ypvhns).)

## Launching the attack

The code of the python script can be found in the this [repository](https://github.com/Wiibleyde/MITM). 

To launch the attack, you need to run the following command:
```bash
sudo python3 main.py
```

After starting the program, you will be asked to enter the IP address of the victim and the IP address of the router.

And that's it, the attack is launched.
If the user selected try to access to "google.com", the program will redirect him to the IP address of the attacker.

## Stopping the attack

To stop the attack, you need to run the following command:
```bash
ps -ef
```

You will see a list of processes, you need to find the process with the name "mitm.py" and kill it. by running the following command:
```bash
sudo kill <PID>
```

## How it works

The program is composed of 2 parts:
1) First part : MITM
    - The first part is a network scanner that will scan the network to find the IP address of the victim and the IP address of the router. 
    - The program will also find the MAC address of the victim and the MAC address of the router. 
    - You can find a pcap file [here](./assets/arp_poisoning.pcap), that show the arp poisoning attack.
2) Second part : DNS Spoofing
    - The second part is a DNS spoofing attack. 
    - The program will redirect the user to the IP address of the attacker when he try to access to "google.com". 
    - To show you :
    ```bash
    $ dig google.com
    nathan@machine:~$ dig google.com
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
## The python script

### Libraries

The python script use to work the following libraries:
- [Scapy](https://scapy.net/) : Used to send and receive packets. 
- [ipaddress](https://docs.python.org/3/library/ipaddress.html) : Used to check if the IP address entered by the user is valid and also get the IP address of the attacker.
- [socket](https://docs.python.org/3/library/socket.html) : Used to get the network mask.
- [fcntl](https://docs.python.org/3/library/fcntl.html) : Used to get the network mask.
- [struct](https://docs.python.org/3/library/struct.html) : Used to get the network mask.
- [os](https://docs.python.org/3/library/os.html) : Used to get the name of the machine to differentiate my computer and the VM.

### Functions

The python script use the following functions:
- `interfaceChecker()` : Check the name of the computer and return the interface name (it depend of the machine).
- `getNetworkMask()` : Get the network mask of the network.
- `makeARPRequest()` : Sends an ARP request to the network and returns the answered and unanswered packets.
- `forwardDnsSpoofing()` : Forward the DNS request to the DNS server and return the DNS response.
    - `forwardDNS()` : Forward the DNS request to the DNS server.
- `main()` : Main function of the program.

### Problems encountered

- During the developpement I had difficulties to find the doc of the library Scapy. 
- I also had difficulties with the priorities of answers of the box DNS and the attacker DNS