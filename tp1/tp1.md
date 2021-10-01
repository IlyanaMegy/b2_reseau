# B1 Réseau 2019 - TP1

## I. Exploration locale en solo


*🌞 Affichez les infos des cartes réseau de votre PC*

    Wireless LAN adapter WiFi:
    
       Connection-specific DNS Suffix  . : home
       Physical Address. . . . . . . . . : C0-E4-34-1F-44-35
	   IPv4 Address. . . . . . . . . . . : 192.168.1.30(Preferred)


    Ethernet adapter Ethernet:
    
       Connection-specific DNS Suffix  . :
       Description . . . . . . . . . . . : Realtek PCIe GbE Family Controller
       Physical Address. . . . . . . . . : 04-0E-3C-EE-03-6B
       IPv4 Address. . . . . . . . . . . : 192.168.137.1(Preferred)
  

     
*🌞 Affichez votre gateway*


    Wireless LAN adapter WiFi:
    
       Connection-specific DNS Suffix  . : home
       Default Gateway . . . . . . . . . : 192.168.1.254



*🌞 Trouvez comment afficher les informations sur une carte IP (change selon l'OS)*

Control Panel\Network and Internet\Network and Sharing Centre

![](https://cdn.discordapp.com/attachments/889061317321838627/889061867429969940/gui_ip.png)

*🌞 Utilisez l'interface graphique de votre OS pour **changer d'adresse IP** :*

**avant :** 
![BEFORE](https://cdn.discordapp.com/attachments/889061317321838627/889064343700897902/gui_ip.png)

**après :** 
![AFTER](https://cdn.discordapp.com/attachments/889061317321838627/889064411090800640/after.png)

*🌞 Il est possible que vous perdiez l'accès internet.*

-> **si l'adresse est déjà prise par un autre objet connecté.**


*🌞 Exploration de la table ARP*

    C:\Users\ily>arp -a
        
    Interface: 192.168.1.20 --- 0x11
      Internet Address      Physical Address      Type
      192.168.1.254         44-d4-54-8d-52-35     dynamic

**-> Se trouve dans le même réseau 192.168.1.\* que l'adresse ip de mon pc.**
**-> Occupe la dernière adresse ip disponible dans ce réseau.**

*🌞 Et si on remplissait un peu la table ?*


    C:\Users\ily>ping 192.168.1.3
    Pinging 192.168.1.3 with 32 bytes of data:
    Reply from 192.168.1.3: bytes=32 time=32ms TTL=64
    Reply from 192.168.1.3: bytes=32 time=36ms TTL=64
    
    C:\Users\ily>ping 192.168.1.254
    Pinging 192.168.1.254 with 32 bytes of data:
    Reply from 192.168.1.254: bytes=32 time=7ms TTL=64
    Reply from 192.168.1.254: bytes=32 time=2ms TTL=64
    
    C:\Users\ily>ping 192.168.1.20
    Pinging 192.168.1.20 with 32 bytes of data:
    Reply from 192.168.1.20: bytes=32 time<1ms TTL=128
    Reply from 192.168.1.20: bytes=32 time<1ms TTL=128


*🌞Utilisez `nmap` pour scanner le réseau de votre carte WiFi et trouver une adresse IP libre*

    C:\Users\ily>nmap -sP 192.168.1.30/24
    Starting Nmap 7.92 ( https://nmap.org ) at 2021-09-19 11:10 Romance Summer Time
    Nmap scan report for 192.168.1.3
    Host is up (0.071s latency).
    MAC Address: EC:6C:9A:D3:33:B8 (Arcadyan)
    Nmap scan report for 192.168.1.4
    Host is up (0.11s latency).
    MAC Address: DA:05:E2:6D:80:83 (Unknown)
    Nmap scan report for bouygues (192.168.1.254)
    Host is up (0.0090s latency).
    MAC Address: 44:D4:54:8D:52:35 (Sagemcom Broadband SAS)
    Nmap scan report for 192.168.1.30
    Host is up.
    Nmap done: 256 IP addresses (4 hosts up) scanned in 20.81 seconds


🌞 *Modifiez de nouveau votre adresse IP vers une adresse IP que vous savez libre grâce à `nmap`*

    C:\Users\ily>nmap -sP 192.168.1.20/24
    Starting Nmap 7.92 ( https://nmap.org ) at 2021-09-19 11:16 Romance Summer Time
    mass_dns: warning: Unable to determine any DNS servers. Reverse DNS is disabled. Try using --system-dns or specify valid servers with --dns-servers
    Nmap scan report for 192.168.1.3
    Host is up (0.066s latency).
    MAC Address: EC:6C:9A:D3:33:B8 (Arcadyan)
    Nmap scan report for 192.168.1.254
    Host is up (0.0033s latency).
    MAC Address: 44:D4:54:8D:52:35 (Sagemcom Broadband SAS)
    Nmap scan report for 192.168.1.20
    Host is up.
    Nmap done: 256 IP addresses (3 hosts up) scanned in 9.36 seconds
    
    C:\Users\ily>ping 8.8.8.8
    
    Pinging 8.8.8.8 with 32 bytes of data:
    Reply from 8.8.8.8: bytes=32 time=17ms TTL=115
    Reply from 8.8.8.8: bytes=32 time=5ms TTL=115
    
    Ping statistics for 8.8.8.8:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 5ms, Maximum = 17ms, Average = 8ms

---

## [](#ii-exploration-locale-en-duo)II. Exploration locale en duo

