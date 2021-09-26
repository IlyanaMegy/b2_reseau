# [](#tp2-on-va-router-des-trucs)TP2 : On va router des trucs


## I. ARP


*🌞**Générer des requêtes ARP***

```
[ily@node1 ~]$ arp -a
? (10.2.1.1) at 0a:00:27:00:00:46 [ether] on enp0s8
node2 (10.2.1.12) at 08:00:27:a3:b7:cf [ether] on enp0s8
_gateway (10.0.2.2) at 52:54:00:12:35:02 [ether] on enp0s3
```
---

*🌞**Analyse de trames***

```
[ily@localhost ~]$ sudo ip neigh flush all

[ily@localhost ~]$ arp -a
? (10.2.1.1) at 0a:00:27:00:00:08 [ether] on enp0s8
_gateway (10.0.2.2) at 52:54:00:12:35:02 [ether] on enp0s3

[ily@localhost ~]$ ping node2
PING node2 (10.2.1.12) 56(84) bytes of data.
64 bytes from node2 (10.2.1.12): icmp_seq=1 ttl=64 time=0.609 ms
64 bytes from node2 (10.2.1.12): icmp_seq=2 ttl=64 time=0.417 ms
[...]
^C

[ily@localhost ~]$ sudo tcpdump -i enp0s8 -w file.pcap
[...]
^C
```
```
C:\Users\ily>scp ily@10.2.1.11:/home/ily/file.pcap \Users\ily\Downloads
```
| ordre | type trame  | source                   | destination                |
|-------|-------------|--------------------------|----------------------------|
| 1     | Requête ARP | `node1` `08 00 27 8d 37 5a` | Broadcast `FF:FF:FF:FF:FF` |
| 2     | Réponse ARP | `node2` `08 00 27 a3 b7 cf` | `node1` `08 00 27 8d 37 5a`   |
| 3	 |  Requête ARP | `node2` `08 00 27 a3 b7 cf` | `node1` `08 00 27 8d 37 5a`|
| 4     | Réponse ARP | `node1` `08 00 27 8d 37 5a` | `node2` `08 00 27 a3 b7 cf` |


## II. Routage

*🌞Ajouter les routes statiques nécessaires pour que `node1.net1.tp2` et `marcel.net2.tp2` puissent se `ping`*
```
[ily@router ~]$ ip r s
10.2.1.0/24 dev enp0s8 proto kernel scope link src 10.2.1.254 metric 101
10.2.1.11 via 10.2.1.254 dev enp0s8
10.2.2.0/24 dev enp0s9 proto kernel scope link src 10.2.2.254 metric 102
10.2.2.12 via 10.2.2.254 dev enp0s9

[ily@node1 ~]$ [ily@node1 ~]$ ip r s
10.2.1.0/24 dev enp0s8 proto kernel scope link src 10.2.1.11 metric 100
10.2.2.0/24 via 10.2.1.254 dev enp0s8 proto static metric 100

[ily@toto ~]$ [ily@toto ~]$ ip r s
10.2.1.0/24 via 10.2.2.254 dev enp0s8 proto static metric 100
10.2.2.0/24 dev enp0s8 proto kernel scope link src 10.2.2.12 metric 100

[ily@node1 network-scripts]$ ping 10.2.2.12
PING 10.2.2.12 (10.2.2.12) 56(84) bytes of data.
64 bytes from 10.2.2.12: icmp_seq=1 ttl=63 time=1.88 ms
64 bytes from 10.2.2.12: icmp_seq=2 ttl=63 time=0.862 ms

[ily@toto ~]$ [ily@toto ~]$ ping 10.2.1.11
PING 10.2.1.11 (10.2.1.11) 56(84) bytes of data.
64 bytes from 10.2.1.11: icmp_seq=1 ttl=63 time=0.942 ms
64 bytes from 10.2.1.11: icmp_seq=2 ttl=63 time=0.831 ms
```

| ordre | type trame  | source | MAC source| destination| MAC dest |
|-------|-------------|--------------------------|----------------------------|--------------------------|----------------------------|
| 1     | Requête ARP | **node1**		 `10.2.1.11`| `08 00 27 8d 37 5a`| **Broadcast** 					| `FF:FF:FF:FF:FF`|
| 2     | Réponse ARP | **router** 	`10.2.1.254`| `08:00:27:e9:14:61`| **node1** 	 `10.2.1.11`|`08 00 27 8d 37 5a`|
| 3	 		| Requête ARP | **router** 	`10.2.2.254`| `08:00:27:6b:65:9b`| **Broadcast** 					| `FF:FF:FF:FF:FF`|
| 4     | Réponse ARP | **toto** 		 `10.2.2.12`| `08:00:27:30:74:34`| **router** `10.2.2.254`| `08:00:27:6b:65:9b`|
| 5     | Ping 				| **node1** 	 `10.2.1.11`| `08 00 27 8d 37 5a`| **toto** 	 `10.2.2.12`| `08:00:27:30:74:34` |
| 6     | Pong			  | **toto** 	   `10.2.2.12`| `08:00:27:30:74:34`| **node1** 	 `10.2.1.11`|`08 00 27 8d 37 5a`|

---
*🌞Donnez un accès internet à vos machines*

```
sudo ip route add default via 10.2.1.254 dev enp0s8

[ily@node1 ~]$ ip r s
default via 10.2.1.254 dev enp0s8

[ily@node1 ~]$ ping 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=110 time=140 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=110 time=86.9 ms
```
```
[ily@toto ~]$ sudo ip route add default via 10.2.2.254 dev enp0s8

[ily@toto ~]$ ping 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=110 time=258 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=110 time=56.8 ms
```

```
[ily@toto ~]$ dig google.com
; <<>> DiG 9.11.26-RedHat-9.11.26-4.el8_4 <<>> google.com

[ily@toto ~]$ ping google.com
PING google.com (142.250.178.142) 56(84) bytes of data.
64 bytes from par21s22-in-f14.1e100.net (142.250.178.142): icmp_seq=1 ttl=109 time=45.5 ms
64 bytes from par21s22-in-f14.1e100.net (142.250.178.142): icmp_seq=2 ttl=109 time=52.5 ms
```
```
[ily@node1 ~]$ [ily@node1 ~]$ dig google.com
; <<>> DiG 9.11.26-RedHat-9.11.26-4.el8_4 <<>> google.com

[ily@node1 ~]$ ping google.com
PING google.com (142.250.179.110) 56(84) bytes of data.
64 bytes from par21s20-in-f14.1e100.net (142.250.179.110): icmp_seq=1 ttl=111 time=103 ms
64 bytes from par21s20-in-f14.1e100.net (142.250.179.110): icmp_seq=2 ttl=111 time=52.1 ms
```
---

*🌞Analyse de trames*


| ordre | type trame  | IP source | MAC source                 | IP destination | MAC destination            |
|-------|-------------|-----------|----------------------------|----------------|---------------------------|
| 1     | Ping        | 10.2.1.11 | `node1` `08:00:27:8d:37:5a`| 8.8.8.8        |`router``08:00:27:e9:14:61` |
| 2     | Pong        | 8.8.8.8   | `router``08:00:27:e9:14:61`| 10.2.1.11      | `node1``08:00:27:8d:37:5a` |

## III. DHCP

    sudo cat /etc/dhcp/dhcpd.conf
    [sudo] password for ily:
    default-lease-time 600;
    max-lease-time 7200;
    
    authoritative;
    subnet 10.2.1.0 netmask 255.255.255.0 {
        range dynamic-bootp 10.2.1.20 10.2.1.253;
        option domain-name-servers 1.1.1.1;
        option subnet-mask 255.255.255.0;
        option routers 10.2.1.254;
    }

-> Je me suis arrêtée là parce que il y avait un problème d'assignation d'IP sur node2; le server lui assigne systématiquement l'IP de node1.
