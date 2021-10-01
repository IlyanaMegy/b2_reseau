    


# TP3 : Progressons vers le réseau d'infrastructure

  

## I. (mini)Architecture réseau

  

**1. Adressage**

  

![enter image description here](https://cdn.discordapp.com/attachments/889061317321838627/893070227036860496/net.png)

| Nom du réseau | Adresse du réseau | Masque | Nombre de clients possibles | Adresse passerelle | Adresse broadcast |
|---------------|-------------------|---------------|-----------------------------|--------------------|----------------------------------------------------------------------------------------------|
| `server1` | `10.3.1.0` | `255.255.255.128` | 128 | `10.3.1.126` | `10.3.1.127` |
| `client1` | `10.3.1.128` | `255.255.255.192` | 64 | `10.3.1.190` | `10.3.1.191` |
| `server2` | `10.3.1.192` | `255.255.255.240` | 16 | `10.3.1.206` | `10.3.1.207`
  

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