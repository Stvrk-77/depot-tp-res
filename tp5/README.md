# TP5 : Un ptit LAN à nous

Premier TP avec des machines virtuelles ! C'est ce qu'on va avoir de mieux pour connecter plusieurs machines dans un réseau local (LAN en anglais), en maîtrisant tout ce qu'il se passe dans ce LAN.

**Ce qu'il y a de plus pratique pour tester des machins !** Habituez-vous :d

> *On ne sera donc pas tributaire d'un équipement un peu "boîte noire" comme dans les TPs précédents : on dépendait toujours de votre partage de co, du routeur de YNOV ou de chez vous, etc.*

![Router](./img/router.jpg)

## Sommaire

- [TP5 : Un ptit LAN à nous](#tp5--un-ptit-lan-à-nous)
  - [Sommaire](#sommaire)
- [0. Prérequis](#0-prérequis)
- [I. Setup](#i-setup)
- [II. Accès internet pour tous](#ii-accès-internet-pour-tous)
  - [1. Accès internet routeur](#1-accès-internet-routeur)
  - [2. Accès internet clients](#2-accès-internet-clients)
- [III. Serveur SSH](#iii-serveur-ssh)
- [IV. Serveur DHCP](#iv-serveur-dhcp)
  - [1. Le but](#1-le-but)
  - [2. Comment le faire](#2-comment-le-faire)
  - [3. Rendu attendu](#3-rendu-attendu)
    - [A. Installation et configuration du serveur DHCP](#a-installation-et-configuration-du-serveur-dhcp)
    - [B. Test avec un nouveau client](#b-test-avec-un-nouveau-client)
    - [C. Consulter le bail DHCP](#c-consulter-le-bail-dhcp)

# 0. Prérequis

➜ **une machine Rocky Linux prête à être clonée**

- on s'en servira pour tout ce qui est routeur/serveur etc.
- pour les ressources, on peut aller sur quelque chose de minimal ça fonctionnera très bien :
  - 1 CPU
  - 1 ou 2G de RAM
- **au moment de cloner, indiquez bien que vous voulez regénérer de nouvelles adresses MAC**

➜ **votre machine Ubuntu prête à être clonée**

- celle que vous avez créée dans un précédent cours, que j'ai vu sur vos PCs
- on s'en servira comme client
- idem, inutile de donner trop de ressources
  - 1-4G de RAM vu que c'est un OS avec une interface graphique
  - mémoire vidéo à fond (128M)
  - 1 CPU ou 2
- **au moment de cloner, indiquez bien que vous voulez regénérer de nouvelles adresses MAC**

➜ **Munissez-vous des ptits mémos que je vous ai préparés :d**

- pour toutes les configurations à effectuer, je vous file toutes les commandes/syntaxes/fichiers etc.
  - [pour les machines Ubuntu c'est ici](../../cours/memo/ubuntu.md)
  - [pour Rocky c'est làààà](../../cours/memo/rocky.md)

# I. Setup

➜ **Mettre en place le setup suivant :**

![Lab 1 TP5](./img/lab1_tp5.png)

➜ **Ptit tableau avec les adresses IP**

| Nom de la machine | IP dans le LAN `10.5.1.0/24` |
|-------------------|------------------------------|
| Votre PC          | `10.5.1.1`                   |
| `client1.tp5.b1`  | `10.5.1.11`                  |
| `client2.tp5.b1`  | `10.5.1.12`                  |
| `routeur.tp5.b1`  | `10.5.1.254`                 |

> `/24` correspond à un masque de `255.255.255.0`. C'est deux façons différentes d'écrire la même chose, on voit ça bientôt. Il faudra indiquer le même masque à toutes les machines.

➜ **Il faut donc, sur votre PC :**

- créer un host-only (réseau privé-hôte en français)
- définir l'IP `10.5.1.1/24` pour le PC depuis l'interface de VBox
- ajouter les cartes réseau nécessaires à toutes les VMs
  - 1 carte host-only pour les 3 VMs (clients et routeur)
  - 1 carte NAT en + pour le routeur

> *Pour rappel la carte host-only, comme montrée sur le schéma, permet que tout le monde soit connecté à un switch virtuel, et ainsi former un LAN (réseau local). La carte NAT permet uniquement un accès internet.*

➜ **Ensuite, pour chaque VM...**

- **configurer l'adresse IP demandée**
  - ça se fait depuis la VM directement : chaque client choisit sa propre IP comme toujours !
  - mettez l'IP indiquée dans le tableau, et le même masque pour tout le monde
- **configurer un *hostname* pour la VM**
  - comme ça, quand on est dans un terminal, le nom de la machin est affiché, et on sait où on est !
  - c'est affiché dans le prompt dans votre terminal : `[it4@localhost]$`
  - le nom par défaut c'est `localhost` et c'est pourri !

☀️ **Uniquement avec des commandes, prouvez-que :**

- vous avez bien configuré les adresses IP demandées (un `ip a` suffit hein)

```vboxuser@vbox:~$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host noprefixroute 
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:37:a5:e5 brd ff:ff:ff:ff:ff:ff
    inet 10.5.1.11/24 brd 10.5.1.255 scope global enp0s3
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fe37:a5e5/64 scope link 
       valid_lft forever preferred_lft forever
```
- vous avez bien configuré les *hostnames* demandés
```vboxuser@vbox:~$  sudo hostnamectl
 Static hostname: client1.tp5.b1
       Icon name: computer-vm
         Chassis: vm 🖴
      Machine ID: 193f35fe49e04bc9896402534a119f6c
         Boot ID: 1f12ff14266e4b2eaf139d95a3b4b38d
  Virtualization: oracle
Operating System: Debian GNU/Linux 12 (bookworm)  
          Kernel: Linux 6.1.0-25-amd64
    Architecture: x86-64
 Hardware Vendor: innotek GmbH
  Hardware Model: VirtualBox
Firmware Version: VirtualBox
vboxuser@vbox:~$ hostnamectl
``
 Static hostname: client2.tp5.b1
       Icon name: computer-vm
         Chassis: vm 🖴
      Machine ID: 193f35fe49e04bc9896402534a119f6c
         Boot ID: d06983345f2148429e7791bcd37adb52
  Virtualization: oracle
Operating System: Debian GNU/Linux 12 (bookworm)  
          Kernel: Linux 6.1.0-25-amd64
    Architecture: x86-64
 Hardware Vendor: innotek GmbH
  Hardware Model: VirtualBox
Firmware Version: VirtualBox
``
[root@routeur ~]# hostnamectl
 Static hostname: routeur.tp5.b1
       Icon name: computer-vm
         Chassis: vm 🖴
      Machine ID: f76bf1e821f14c1db8df6077e717a3f2
         Boot ID: 43b586986809456da2d6804e6ac10bfb
  Virtualization: oracle
Operating System: Rocky Linux 9.4 (Blue Onyx)
     CPE OS Name: cpe:/o:rocky:rocky:9::baseos
          Kernel: Linux 5.14.0-427.13.1.el9_4.x86_64
    Architecture: x86-64
 Hardware Vendor: innotek GmbH
  Hardware Model: VirtualBox
Firmware Version: VirtualBox
``

 
```
- tout le monde peut se ping au sein du réseau `10.5.1.0/24`
```
vboxuser@client1:~$ ping 10.5.1.254
PING 10.5.1.254 (10.5.1.254) 56(84) bytes of data.
64 bytes from 10.5.1.254: icmp_seq=1 ttl=64 time=7.50 ms
64 bytes from 10.5.1.254: icmp_seq=2 ttl=64 time=1.19 ms
64 bytes from 10.5.1.254: icmp_seq=3 ttl=64 time=1.43 ms
64 bytes from 10.5.1.254: icmp_seq=4 ttl=64 time=1.17 ms
64 bytes from 10.5.1.254: icmp_seq=5 ttl=64 time=1.22 ms
64 bytes from 10.5.1.254: icmp_seq=6 ttl=64 time=1.52 ms
^C
--- 10.5.1.254 ping statistics ---
6 packets transmitted, 6 received, 0% packet loss, time 5044ms
rtt min/avg/max/mdev = 1.166/2.337/7.501/2.312 ms

```

> *Dès que votre PC peut `ping` tout le monde, je vous conseille **fortement** de vous connecter en SSH à vos VMs. En particulier la VM Rocky qui a déjà un serveur SSH qui tourne par défaut.*

# II. Accès internet pour tous

➜ **Actuellement, tout le monde est connecté, mais les clients n'ont pas internet !**

![Conneted, no internet](./img/connected_no_internet.png)

➜ Dans cette partie, on va faire en sorte que tout le monde ait un accès internet :

- le routeur a déjà un accès internet
- les clients vont se servir du routeur comme passerelle afin d'accéder à internet

Pour ça :

- il faut que notre machine `routeur.tp5.b1` accepte de router des paquets
- il faut que chaque client
  - connaisse `routeur.tp.b1` comme sa passerelle (`10.5.1.254`)
  - connaisse l'adresse d'un serveur DNS (pour résoudre des noms comme `www.ynov.com` afin de connaître l'adresse IP associée à ce nom)

> Si l'un de ces points n'est pas correctement configuré, on est bien "connectés" (genre y'a un LAN, tout le monde se `ping`) mais sans "accès internet".

## 1. Accès internet routeur

> *Cette section 1. est à réaliser sur `routeur.tp5.b1`.*

☀️ **Déjà, prouvez que le routeur a un accès internet**

- une seule commande `ping` suffit à prouver ça, vers un nom de domaine que vous connaissez, genre `www.ynov.com` (ou autre de votre choix :d)
```
[stark@routeur ~]$ ping ynov.com
PING ynov.com (172.67.74.226) 56(84) bytes of data.
64 bytes from 172.67.74.226 (172.67.74.226): icmp_seq=1 ttl=255 time=567 ms
64 bytes from 172.67.74.226 (172.67.74.226): icmp_seq=2 ttl=255 time=757 ms
64 bytes from 172.67.74.226 (172.67.74.226): icmp_seq=3 ttl=255 time=720 ms
64 bytes from 172.67.74.226 (172.67.74.226): icmp_seq=4 ttl=255 time=514 ms
64 bytes from 172.67.74.226 (172.67.74.226): icmp_seq=5 ttl=255 time=515 ms
64 bytes from 172.67.74.226 (172.67.74.226): icmp_seq=6 ttl=255 time=515 ms
^C
--- ynov.com ping statistics ---
7 packets transmitted, 6 received, 14.2857% packet loss, time 12316ms
rtt min/avg/max/mdev = 513.821/598.047/757.078/101.497 ms
```

☀️ **Activez le routage**
```
[root@routeur ~]# sudo firewall-cmd --add-masquerade --permanent
Warning: ALREADY_ENABLED: masquerade
success
[root@routeur ~]# sudo firewall-cmd --add-masquerade --permanent
Warning: ALREADY_ENABLED: masquerade
success
[root@routeur ~]# sudo firewall-cmd --reload
success
```

## 2. Accès internet clients

> *Cette section 2. est à réaliser sur `client1.tp5.b1` et `client2.tp5.b1`. Tout est dans [le mémo réseau Ubuntu](../../cours/memo/ubuntu.md).*

➜ **Définir l'adresse IP du routeur comme passerelle pour les clients**
```
vboxuser@client1:~$ ip r s
default via 10.5.1.254 dev enp0s3 onlink 
10.5.1.0/24 dev enp0s3 proto kernel scope link src 10.5.1.11 
169.254.0.0/16 dev enp0s3 scope link metric 1000 
``

vboxuser@vbox:~$ ip r s
default via 10.5.1.254 dev enp0s3 onlink 
10.5.1.0/24 dev enp0s3 proto kernel scope link src 10.5.1.12 
169.254.0.0/16 dev enp0s3 scope link metric 1000 
```

➜ **Vérifier que les clients ont un accès internet**

- avec un `ping` vers une adresse IP publique vous connaissez
- à ce stade, vos clients ne peuvent toujours pas résoudre des noms, donc impossible de visiter un site comme `www.ynov.com`

➜ **Définir `1.1.1.1` comme serveur DNS que peuvent utiliser les clients**

- redémarrez l'interface réseau si nécessaire pour que ça prenne effet
- ainsi vos clients pourront spontanément envoyer des requêtes DNS vers `1.1.1.1` afin d'apprendre à quelle IP correspond un nom de domaine donné

> *`1.1.1.1` c'est l'adresse IP publique d'un serveur DNS d'une entreprise qui s'appelle CloudFlare (un gros acteur du Web). Ils hébergent gracieusement et publiquement ce serveur DNS, afin que n'importe qui puisse l'utiliser.*

☀️ **Prouvez que les clients ont un accès internet**

```
vboxuser@client1:~$ ping ynov.com
PING ynov.com (104.26.10.233) 56(84) bytes of data.
64 bytes from 104.26.10.233 (104.26.10.233): icmp_seq=1 ttl=254 time=612 ms
64 bytes from 104.26.10.233 (104.26.10.233): icmp_seq=2 ttl=254 time=535 ms
64 bytes from 104.26.10.233 (104.26.10.233): icmp_seq=3 ttl=254 time=531 ms
^C64 bytes from 104.26.10.233: icmp_seq=4 ttl=254 time=532 ms

--- ynov.com ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3309ms
rtt min/avg/max/mdev = 531.411/552.792/612.422/34.451 ms
```

☀️ **Montrez-moi le contenu final du fichier de configuration de l'interface réseau**
```
vboxuser@vbox:~$ cat /etc/network/interfaces
# This file describes the network interfaces available on your system
# and how to activate them. For more information, see interfaces(5).

source /etc/network/interfaces.d/*

# The loopback network interface
auto lo
iface lo inet loopback
auto enp0s3
iface enp0s3 inet static
  address 10.5.1.12/24
  gateway 10.5.1.254
```
# III. Serveur SSH

> *Cette partie III. est à réaliser sur `routeur.tp5.b1`. Tout est dans [le mémo réseau Rocky](../../cours/memo/rocky.md).*

☀️ **Sur `routeur.tp5.b1`, déterminer sur quel port écoute le serveur SSH**
```
[root@routeur ~]# sudo ss -lnpt| grep 22
LISTEN 0      128          0.0.0.0:22        0.0.0.0:*    users:(("sshd",pid=695,fd=3))
LISTEN 0      128             [::]:22           [::]:*    users:(("sshd",pid=695,fd=4))
☀️ **Sur `routeur.tp5.b1`, vérifier que ce port est bien ouvert**
```
# IV. Serveur DHCP

## 1. Le but

➜ On installe et configure **notre propre serveur DHCP** dans cette partie ! Le but est le suivant :

- **dès qu'un client se connecte à notre réseau, il a automatiquement internet !**
- ça **évite de faire à la main** comme vous avez fait dans ce TP :
  - choisir et configurer une adresse IP
  - choisir et configurer l'adresse d'un serveur DNS
  - configurer l'adresse de la passerelle
- **dès qu'il se connecte, il essaiera automatiquement de contacter un serveur DHCP**
- **notre serveur DHCP lui proposera alors automatiquement tout le nécessaire pour avoir un accès internet**, à savoir :
  - une adresse IP disponible
  - l'adresse d'un serveur DNS
  - l'adresse de la passerelle du réseau

![DHCP server](./img/dhcp.png)

## 2. Comment le faire

> Cette fois, je vous ré-écris pas tout, je vous laisse chercher sur internet par vous-mêmes "install dhcp server rocky 9", ou vous référer [par exemple à ce lien](https://www.server-world.info/en/note?os=Rocky_Linux_8&p=dhcp&f=1) qui résume très bien la chose.

➜ Peu importe le lien que vous suivez, **les étapes seront les suivantes** :

- installation du paquet qui contient le serveur DHCP
  - commande `dnf install`
- modification de la configuration
  - c'est un fichier texte (comme toujours)
  - donc avec `nano` ou `vim` par exemple
- (re)démarrage du service DHCP
  - avec un `systemctl start`

➜ **Et si ça fonctionne pas, c'est que tu t'es planté dans le fichier de conf, donc tu vas lire pourquoi dans les logs** :

- voir les logs d'erreur
  - avec une commande `journalctl`
  - généralement, il dit clairement l'erreur
- ajustement de la configuration
  - c'est un fichier texte (comme toujours)
  - donc avec `nano` ou `vim` par exemple
- redémarrage du service DHCP
  - avec un `systemctl restart`

> N'hésitez pas, comme d'hab, à m'appeler si vous galérez avec cette section !

## 3. Rendu attendu

> *Vous pouvez éteindre `client1.tp5.b1` et `client2.tp5.b1` pour limiter l'utilisation des ressources hein.*

⚠️⚠️⚠️ **Vous n'avez le droit d'utiliser QUE des lignes que vous comprenez dans le fichier de configuration.** Et pour lesquelles vous avez adapté les valeurs au TP. **Vous devez enlever les lignes de configuration inutiles pour notre TP.**

### A. Installation et configuration du serveur DHCP

> *Cette section A. est à réaliser sur `routeur.tp5.b1`.*

☀️ **Installez et configurez un serveur DHCP sur la machine `routeur.tp5.b1`**

```
[root@routeur ~]# dnf install dhcp
[root@routeur ~]# dnf -y install dhcp-server
[root@routeur ~]# sudo nano dhcpd.conf
```

```
}# specify DNS server's hostname or IP address
option domain-name-servers  1.1.1.1;
# specify network address and subnetmask
subnet 10.5.1.0 netmask 255.255.255.0 {
    # specify the range of lease IP address
    range dynamic-bootp 10.5.1.137 10.5.1.237;
    # specify broadcast address
    option broadcast-address 10.5.1.255;
    # specify gateway
    option routers 10.5.1.254;
}
```

```
[root@routeur ~]# sudo systemctl restart dhcpd
```
### B. Test avec un nouveau client


☀️ **Créez une nouvelle machine client `client3.tp5.b1`**

- définissez son hostname
```
vboxuser@client3tp5:~$ hostnamectl
 Static hostname: client3tp5.b1
       Icon name: computer-vm
         Chassis: vm 🖴
      Machine ID: 193f35fe49e04bc9896402534a119f6c
         Boot ID: a2c7eb5222674746ab50914f148210e8
  Virtualization: oracle
Operating System: Debian GNU/Linux 12 (bookworm)  
          Kernel: Linux 6.1.0-25-amd64
    Architecture: x86-64
 Hardware Vendor: innotek GmbH
  Hardware Model: VirtualBox
Firmware Version: VirtualBox
```
- définissez une IP en DHCP 

```
vboxuser@client3tp5:~$ cat /etc/network/interfaces
# This file describes the network interfaces available on your system
# and how to activate them. For more information, see interfaces(5).

source /etc/network/interfaces.d/*

# The loopback network interface
auto lo
iface lo inet loopback
auto enp0s3
iface enp0s3 inet dhcp
``` 
- vérifiez que c'est bien une adresse IP entre `.137` et `.237`
```
vboxuser@client3tp5:~$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host noprefixroute 
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:fc:0c:6f brd ff:ff:ff:ff:ff:ff
    inet 10.5.1.137/24 brd 10.5.1.255 scope global dynamic enp0s3
       valid_lft 43197sec preferred_lft 43197sec
    inet6 fe80::a00:27ff:fefc:c6f/64 scope link 
       valid_lft forever preferred_lft forever
```
- prouvez qu'il a immédiatement un accès internet

```
vboxuser@client3tp5:~$ ping ynov.com
PING ynov.com (104.26.11.233) 56(84) bytes of data.
64 bytes from 104.26.11.233 (104.26.11.233): icmp_seq=1 ttl=254 time=515 ms
64 bytes from 104.26.11.233 (104.26.11.233): icmp_seq=2 ttl=254 time=541 ms
64 bytes from 104.26.11.233 (104.26.11.233): icmp_seq=3 ttl=254 time=522 ms
64 bytes from 104.26.11.233 (104.26.11.233): icmp_seq=4 ttl=254 time=517 ms
^C64 bytes from 104.26.11.233: icmp_seq=5 ttl=254 time=515 ms

--- ynov.com ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4195ms
rtt min/avg/max/mdev = 514.887/521.989/540.605/9.603 ms
```

### C. Consulter le bail DHCP

➜ **Côté serveur DHCP, à chaque fois qu'une adresse IP est proposée à quelqu'un, le serveur crée un fichier texte appelé *bail DHCP*** (ou *DHCP lease* en anglais).

Il contient toutes les informations liées à l'échange avec le client, notamment :

- adresse MAC du client qui a demandé l'IP
- adresse IP proposée au client
- heure et date précises de l'échange DHCP
- durée de validité du *bail DHCP*

> *A l'issue de cette durée de validité, le client devra de nouveau contacter le serveur, pour s'assurer que l'adresse IP est toujours libre. Rappelez-vous que DHCP est utilisé partout pour attribuer automatiquement des adresses IP aux clients, à l'école, chez vous, etc. C'est nécessaire que le bail expire pour pas qu'un client qui se connecte une seule fois monopolise à vie une adresse IP.*

☀️ **Consultez le *bail DHCP* qui a été créé pour notre client**
```
[root@routeur ~]# cat /var/lib/dhcpd/dhcpd.leases

# The format of this file is documented in the dhcpd.leases(5) manual page.
# This lease file was written by isc-dhcp-4.4.2b1

# authoring-byte-order entry is generated, DO NOT DELETE
authoring-byte-order little-endian;

lease 10.5.1.137 {
  starts 4 2024/10/17 00:29:37;
  ends 4 2024/10/17 12:29:37;
  cltt 4 2024/10/17 00:29:37;
  binding state active;
  next binding state free;
  rewind binding state free;
  hardware ethernet 08:00:27:fc:0c:6f;
  uid "\377'\374\014o\000\001\000\001.\243\201\372\010\000'\374\014o";
  client-hostname "client3tp5.b1";
}
```
☀️ **Confirmez qu'il s'agit bien de la bonne adresse MAC**
```
vboxuser@client3tp5:~$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host noprefixroute 
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:fc:0c:6f brd ff:ff:ff:ff:ff:ff
    inet 10.5.1.137/24 brd 10.5.1.255 scope global dynamic enp0s3
       valid_lft 38440sec preferred_lft 38440sec
    inet6 fe80::a00:27ff:fefc:c6f/64 scope link 
       valid_lft forever preferred_lft forever
```