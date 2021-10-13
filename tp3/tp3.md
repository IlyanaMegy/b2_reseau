    


# TP3 : Progressons vers le réseau d'infrastructure

## I. (mini)Architecture réseau

**1. Adressage**  

![enter image description here](https://cdn.discordapp.com/attachments/889061317321838627/893070227036860496/net.png)

| Nom du réseau | Adresse du réseau | Masque | Nombre de clients possibles | Adresse passerelle | Adresse broadcast |
|---------------|-------------------|---------------|-----------------------------|--------------------|----------------------------------------------------------------------------------------------|
| `server1` | `10.3.1.0` | `255.255.255.128` | 128 | `10.3.1.126` | `10.3.1.127` |
| `client1` | `10.3.1.128` | `255.255.255.192` | 64 | `10.3.1.190` | `10.3.1.191` |
| `server2` | `10.3.1.192` | `255.255.255.240` | 16 | `10.3.1.206` | `10.3.1.207` |

---

|Nom machine|Adresse dans`client1`|Adresse dans `server1`|Adresse dans `server2`|Adresse passerelle|
|-----------|---------------------|----------------------|----------------------|------------------|
|`router.tp3` |`10.3.1.190/26`|`10.3.1.126/25`|`10.3.1.206/28`|Carte NAT|
|`dhcp_client1.tp3`|`10.3.1.189/26`|-|-|`10.3.1.190/26`|
|`marcel`|DHCP|-|-|`10.3.1.190/26`|
|`dns1.server1.tp3`|`10.3.1.125/25`|-|-|`10.3.1.126/25`|
|`johnny.client1.tp3`|DHCP|-|-|`10.3.1.190/26`|

  
  ---

**2. Routeur**

    [ily@router ~]$ ip a
    2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
       inet 10.0.2.15/24 brd 10.0.2.255 scope global dynamic noprefixroute enp0s3
    3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
        inet 10.3.1.62/26 brd 10.3.1.63 scope global noprefixroute enp0s8
    4: enp0s9: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
        inet 10.3.1.190/25 brd 10.3.1.255 scope global noprefixroute enp0s9
    5: enp0s10: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
        inet 10.3.1.206/28 brd 10.3.1.207 scope global noprefixroute enp0s10
           valid_lft forever preferred_lft forever
        inet6 fe80::a00:27ff:feb7:8793/64 scope link
           valid_lft forever preferred_lft forever

    [ily@router ~]$ dig google.com
    
    ; <<>> DiG 9.11.26-RedHat-9.11.26-4.el8_4 <<>> google.com
    ;; global options: +cmd
    ;; Got answer:
    ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 34943
    ;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1
    
    ;; OPT PSEUDOSECTION:
    ; EDNS: version: 0, flags:; udp: 512
    ;; QUESTION SECTION:
    ;google.com.                    IN      A
    
    ;; ANSWER SECTION:
    google.com.             15      IN      A       216.58.213.78
    

## II. Services d'infra

**1. Serveur DHCP**

    [ily@dhcp_client ~]$ sudo cat /etc/dhcp/dhcpd.conf
    
    default-lease-time 600;
    max-lease-time 7200;
    
    authoritative;
    subnet 10.3.1.128 netmask 255.255.255.192 {
            range dynamic-bootp 10.3.1.130 10.3.1.188;
            option domain-name-servers 1.1.1.1;
            option subnet-mask 255.255.255.192;
            option routers 10.3.1.190;
    }
    
    [ily@dhcp_client ~]$ ip a
    2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
        inet 10.3.1.189/26 brd 10.3.1.191 scope global noprefixroute enp0s3
    

🌞 **Mettre en place un client dans le réseau `client1`**

    [ily@marcel ~]$ ping google.com
    PING google.com (172.217.18.206) 56(84) bytes of data.
    64 bytes from ham02s14-in-f206.1e100.net (172.217.18.206): icmp_seq=1 ttl=112 time=101 ms
    64 bytes from ham02s14-in-f206.1e100.net (172.217.18.206): icmp_seq=2 ttl=112 time=33.9 ms

    [ily@marcel ~]$ arp -a
    router (10.3.1.190) at 08:00:27:fa:90:3a [ether] on enp0s3
    ? (10.3.1.129) at 0a:00:27:00:00:0a [ether] on enp0s3

    [ily@marcel ~]$ traceroute google.com
    traceroute to google.com (142.250.179.110), 30 hops max, 60 byte packets
     1  router (10.3.1.190)  1.953 ms  1.908 ms  1.887 ms
     2  10.0.2.2 (10.0.2.2)  1.827 ms  1.792 ms  1.567 ms

---

**2. Serveur DNS**

🌞 **Mettre en place une machine qui fera office de serveur DNS**

https://kifarunix.com/install-and-setup-bind-dns-server-on-rocky-linux-8/
https://techviewleo.com/configure-bind-master-dns-server-on-rocky-linux/

    [ily@dns1_server1.tp3 named]$ systemctl status named
    ● named.service - Berkeley Internet Name Domain (DNS)
       Loaded: loaded (/usr/lib/systemd/system/named.service; enabled; vendor preset: >
       Active: active (running) since Tue 2021-10-12 12:38:31 CEST; 5h 31min ago
       

🌞 **Tester le DNS depuis `marcel.client1.tp3`**

    [ily@marcel ~]$ dig ynov.com @dns1.server1.tp3
    ; <<>> DiG 9.11.26-RedHat-9.11.26-4.el8_4 <<>> ynov.com @dns1.server1.tp3
    ;; global options: +cmd
    ;; Got answer:
    ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 15626
    ;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 3, ADDITIONAL: 1
    
    ;; OPT PSEUDOSECTION:
    ; EDNS: version: 0, flags:; udp: 1232
    ; COOKIE: 0c85bd21207e3c479677f4b26165ae41784aa2ef0338a95b (good)
    ;; QUESTION SECTION:
    ;ynov.com.                      IN      A
    
    ;; ANSWER SECTION:
    ynov.com.               10511   IN      A       92.243.16.143

		[...]
		
    ;; Query time: 0 msec
    ;; SERVER: 10.3.1.125#53(10.3.1.125)
    ;; WHEN: Tue Oct 12 18:06:59 CEST 2021
    ;; MSG SIZE  rcvd: 158

---

**3. Get deeper**
🌞 **Affiner la configuration du DHCP**



