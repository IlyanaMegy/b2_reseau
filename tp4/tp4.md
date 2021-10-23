# TP4 : Vers un réseau d'entreprise

# Sommaire
 - [3. Setup topologie 1](#3-setup-topologie-1)
- [II. VLAN](#ii-vlan)
 - [3. Setup topologie 2](#3-setup-topologie-2)
- [III. Routing](#iii-routing)
 - [3. Setup topologie 3](#3-setup-topologie-3)


# I. Dumb switch

## 1. Topologie 1

## 2. Adressage topologie 1
| Node  | IP            |
|-------|---------------|
| `pc1` | `10.1.1.1/24` |
| `pc2` | `10.1.1.2/24` |

## 3. Setup topologie 1
🌞 **Commençons simple**
- définissez les IPs statiques sur les deux VPCS
- `ping` un VPCS depuis l'autre

```
pc1> show ip
NAME        : pc1[1]
IP/MASK     : 10.1.1.1/24
GATEWAY     : 255.255.255.0

pc1> ping 10.1.1.2
84 bytes from 10.1.1.2 icmp_seq=1 ttl=64 time=4.400 ms
84 bytes from 10.1.1.2 icmp_seq=2 ttl=64 time=2.416 ms
```
```
pc2> show ip
NAME        : pc2[1]
IP/MASK     : 10.1.1.2/24
GATEWAY     : 255.255.255.0

pc2> ping 10.1.1.1
84 bytes from 10.1.1.1 icmp_seq=1 ttl=64 time=3.895 ms
84 bytes from 10.1.1.1 icmp_seq=2 ttl=64 time=4.303 ms
```

# II. VLAN

## 1. Topologie 2

## 2. Adressage topologie 2
| Node  | IP            | VLAN |
|-------|---------------|------|
| `pc1` | `10.1.1.1/24` | 10   |
| `pc2` | `10.1.1.2/24` | 10   |
| `pc3` | `10.1.1.3/24` | 20   |

### 3. Setup topologie 2
🌞 **Adressage**
- définissez les IPs statiques sur tous les VPCS
- vérifiez avec des `ping` que tout le monde se ping

```
pc1> ping 10.1.1.3
84 bytes from 10.1.1.3 icmp_seq=1 ttl=64 time=4.400 ms
84 bytes from 10.1.1.3 icmp_seq=2 ttl=64 time=2.416 ms

pc2> ping 10.1.1.3
84 bytes from 10.1.1.3 icmp_seq=1 ttl=64 time=3.895 ms
84 bytes from 10.1.1.3 icmp_seq=2 ttl=64 time=4.303 ms
```

🌞 **Configuration des VLANs**

    Switch#show vlan
        
    |VLAN Name	|Status	  |Ports				|
    |-----------|---------|-------------|
    |1 default  |active		|Gi0/2, Gi1/0, Gi1/1, Gi1/2, Gi1/3, Gi2/0, Gi2/1, Gi2/2, Gi2/3, Gi3/0, Gi3/1, Gi3/2,  Gi3/3  |
    |10 vlan10	|active		|Gi0/0, Gi0/1	|
    |20 vlan20	|active		|Gi0/3				|

🌞 **Vérif**
- `pc1` et `pc2` doivent toujours pouvoir se ping
- `pc3` ne ping plus personne

```
pc3> ping 10.1.1.1
host (10.1.1.1) not reachable

pc3> ping 10.1.1.2
host (10.1.1.2) not reachable
```

# III. Routing

## 1. Topologie 3

## 2. Adressage topologie 3
Les réseaux et leurs VLANs associés :
| Réseau    | Adresse       | VLAN associé |
|-----------|---------------|--------------|
| `clients` | `10.1.1.0/24` | 11           |
| `admins`  | `10.2.2.0/24` | 12           |
| `servers` | `10.3.3.0/24` | 13           |

L'adresse des machines au sein de ces réseaux :

| Node               | `clients`       | `admins`        | `servers`       |
|--------------------|-----------------|-----------------|-----------------|
| `pc1.clients.tp4`  | `10.1.1.1/24`   | x               | x               |
| `pc2.clients.tp4`  | `10.1.1.2/24`   | x               | x               |
| `adm1.admins.tp4`  | x               | `10.2.2.1/24`   | x               |
| `web1.servers.tp4` | x               | x               | `10.3.3.1/24`   |
| `r1`               | `10.1.1.254/24` | `10.2.2.254/24` | `10.3.3.254/24` |

## 3. Setup topologie 3
🌞 **Configuration des VLANs**

    Switch>show vlan br
    VLAN Name                             Status    Ports
    ---- -------------------------------- --------- -------------------------------
    1    default                          active    Gi0/3, Gi1/2, Gi1/3, Gi2/0
                                                    Gi2/1, Gi2/2, Gi2/3, Gi3/0
                                                    Gi3/1, Gi3/2, Gi3/3
    11   clients                          active    Gi0/0, Gi0/1
    12   admins                           active    Gi1/0
    13   servers                          active    Gi1/1

---
🌞 **Config du *routeur***

    Router#show ip route
         10.0.0.0/24 is subnetted, 3 subnets
    C       10.3.3.0 is directly connected, FastEthernet0/0.13
    C       10.2.2.0 is directly connected, FastEthernet0/0.12
    C       10.1.1.0 is directly connected, FastEthernet0/0.11

    Router#show ip int br
    Interface                  IP-Address      OK? Method Status                Protocol
    FastEthernet0/0            unassigned      YES unset  up                    up
    FastEthernet0/0.10         unassigned      YES manual deleted               down
    FastEthernet0/0.11         10.1.1.254      YES manual up                    up
    FastEthernet0/0.12         10.2.2.254      YES manual up                    up
    FastEthernet0/0.13         10.3.3.254      YES manual up                    up



