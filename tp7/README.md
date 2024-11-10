# TP7 : On dit chiffrer pas crypter

Dans ce TP on va mettre en place des outils afin de mettre en oeuvre du chiffrement.

Au menuuu :

- serveur Web avec HTTPS
- serveur VPN

![Don't encrypt](./img/dont_encrypt.jpg)

## Sommaire

- [TP7 : On dit chiffrer pas crypter](#tp7--on-dit-chiffrer-pas-crypter)
  - [Sommaire](#sommaire)
- [0. Prérequis](#0-prérequis)
- [I. Setup](#i-setup)
- [II. Serveur Web](#ii-serveur-web)
- [III. Serveur VPN](#iii-serveur-vpn)

# 0. Prérequis

➜ **une machine Rocky Linux prête à être clonée**

- on s'en servira pour tout ce qui est routeur/serveur etc.
- descendez bien à 1CPU et 1Go de RAM pour économiser des ressources

➜ **votre machine Ubuntu prête à être clonée**

- pour le(s) client(s) toujours !
- idem, limitez les ressources (1CPU, 1Go de RAM, 128Mo mémoire vidéo vu que c'est avec interface graphique)

➜ **Ayez [les mémos](../../cours/memo/README.md) sous le coude !**

# I. Setup

➜ Pour chaque machine

- donnez une IP statique
- pour l'accès internet
  - activer le routage sur le routeur
  - carte NAT sur le routeur
  - définir le routeur comme passerelle pour les autres
  - aussi, n'oubliez pas d'indiquer `1.1.1.1` en serveur DNS partout
- nommez vos machines (configuration du hostname)

# II. Serveur Web

# Serveur Web

- [Serveur Web](#serveur-web)
  - [0. Setup](#0-setup)
    - [A. Schéma](#a-schéma)
    - [B. Tableau adressage](#b-tableau-adressage)
  - [1. HTTP](#1-http)
    - [A. Install](#a-install)
    - [B. Configuration](#b-configuration)
    - [C. Tests client](#c-tests-client)
    - [D. Analyze](#d-analyze)
  - [2. On rajoute un S](#2-on-rajoute-un-s)
    - [A. Config](#a-config)
    - [B. Test test test analyyyze](#b-test-test-test-analyyyze)

## 0. Setup

### A. Schéma

![Webz part](./img/part_web.svg)

### B. Tableau adressage

| Machine          | LAN1 `10.7.1.0/24` |
|------------------|--------------------|
| `client1.tp7.b1` | `10.7.1.101`       |
| `web.tp7.b1`     | `10.7.1.11`        |
| `routeur.tp7.b1` | `10.7.1.254`       |

## 1. HTTP

### A. Install

> *Sur `web.tp7.b1`.*

➜ **Installer le service NGINX**

```bash
dnf install -y nginx
```

### B. Configuration

> *Sur `web.tp7.b1`.*

➜ **Créer un fichier de configuration pour un nouveau site web**

- créer un fichier dans le dossier `/etc/nginx/conf.d/` qui a l'extension `.conf`
- on va créer `/etc/nginx/conf.d/sitedefou.tp7.b1.conf`
- avec le contenu suivant (remplacez `<ADRESSE_IP>` par l'adresse IP du serveur web)

```nginx
server {
    # le nom que devront taper les clients pour visiter le site
    server_name   sitedefou.tp7.b1;

    # on indique sur quelle adresse IP et quel port écouter
    listen        <ADRESSE_IP>:80;

    location      / {
        # "root" pour "racine" : l'emplacement de la racine web
        # c'est à dire le dossier qui contient le site web
        root      /var/www/sitedefou.tp7.b1;
    }sudo ss -lnptsudo ss -lnptsudo ss -lnpt
}
```

➜ **Créer un nouveau site web :d**

```bash
# on crée le dossier qui contient le site web
sudo mkdir -p /var/www/sitedefou.tp7.b1

# on dév un gros site web
echo "meow !" | sudo tee /var/www/sitedefou.tp7.b1

# gestion des permissions sur le dossier
sudo chown nginx:nginx -R /var/www/sitedefou.tp7.b1
```

➜ **On démarre le service web NGINX**

```bash
sudo systemctl start nginx
```

🌞 **Lister les ports en écoute sur la machine**
```
[root@web stark]# ss -lnpt | grep 80
LISTEN 0      511          0.0.0.0:80        0.0.0.0:*    users:(("nginx",pid=761,fd=6),("nginx",pid=759,fd=6))
LISTEN 0      511             [::]:80           [::]:*    users:(("nginx",pid=761,fd=7),("nginx",pid=759,fd=7))
```

🌞 **Ouvrir le port dans le firewall de la machine**
```
[root@web stark]# sudo firewall-cmd --permanent --add-port=80/tcp
Warning: ALREADY_ENABLED: 80:tcp
success
[root@web stark]# sudo firewall reload
sudo: firewall: command not found
[root@web stark]# sudo firewall-cmd --reload
success
```

### C. Tests client

> *Sur `client1.tp7.b1`.*

➜ **Ajoutez la ligne suivante au fichier `/etc/hosts`**

```
10.7.1.11 sitedefou.tp7.b1
```

> *Le fichier `/etc/hosts` c'est quand on veut un joli nom qui correspond à une IP, mais qu'on a la flemme de monter un serveur DNS juste pour ça :d*

🌞 **Vérifier que ça a pris effet**

- faites un `ping` vers `sitedefou.tp7.b1`
```
vboxuser@client1:~$ ping sitedefou.tp7.b1
PING sitedefou.tp7.b1 (10.7.1.11) 56(84) bytes of data.
64 bytes from sitedefou.tp7.b1 (10.7.1.11): icmp_seq=1 ttl=64 time=0.035 ms
64 bytes from sitedefou.tp7.b1 (10.7.1.11): icmp_seq=2 ttl=64 time=0.056 ms
64 bytes from sitedefou.tp7.b1 (10.7.1.11): icmp_seq=3 ttl=64 time=0.054 ms
^C64 bytes from sitedefou.tp7.b1 (10.7.1.11): icmp_seq=4 ttl=64 time=0.055 ms
^C
--- sitedefou.tp7.b1 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3028ms
rtt min/avg/max/mdev = 0.035/0.050/0.056/0.008 ms

```
- visitez `http://sitedefou.tp7.b1`
  - pour le compte-rendu, vous pouvez faire un `curl` depuis le terminal
```
  vboxuser@client1:~$ $ curl sitedefou.tp7.b1
<!doctype html>
<html>
  <head>
    <meta charset='utf-8'>
```
- **vous devriez avoir la page "meow !" quand vous visitez le site**

### D. Analyze

> *Sur `client1.tp7.b1`.*

🌞 **Capture `tcp_http.pcap`**
```vboxuser@client1:~$ sudo tcpdump -w tcp_https.pcap -i enp0s8``` 


- **capturer une session TCP complète**
  - le début (SYN, SYN ACK, ACK)
  - des données échangées (PSH, PSH ACK)
    - en l'occurence ce sera votre requête HTTP
    - et la réponse HTTP du serveur (meow !)
  - une fin de session (ça dépend, le plus souvent : FIN, FIN ACK ou RST)
- cette session TCP doit être celle de l'échange HTTP pour récupérer la page du serveur web
- il faut donc capturer pendant que tu visites le site web

> ⚠️⚠️ *Remarquez que vous pouvez voir en clair le texte "meow !" dans la capture Wireshark. Toute la requête HTTP étouétou.*

🌞 **Voir la connexion établie**

- utilisez une commande `ss` sur le client pour voir le tunnel TCP établi
- il faut taper la commande au moment où vous visitez le site web (ou juste après :d)
````
[root@web ~]# ss -lnpt |grep 80
LISTEN 0      511          0.0.0.0:80        0.0.0.0:*    users:(("nginx",pid=1452,fd=6),("nginx",pid=1451,fd=6))
LISTEN 0      511             [::]:80           [::]:*    users:(("nginx",pid=1452,fd=7),("nginx",pid=1451,fd=7))
````

## 2. On rajoute un S

### A. Config

> *Sur `web.tp7.b1`.*

➜ **Générer une paire de clé pour le chiffrement**

- l'une des deux sera stocké dans un certificat
- suivez juste le guide :d

```bash
# générer une paire de clé
openssl req -new -newkey rsa:4096 -days 365 -nodes -x509 -keyout server.key -out server.crt

# déplacer la clé privée dans un endroit moins pourri
# il existe sur tous les OS un dossier destiné à stocker des clés de chiffrement
sudo mv server.key /etc/ssl/private/sitedefou.tp7.b1.key
# idem pour le certificat (qui contient la clé publique)
sudo mv server.crt /etc/ssl/certs/sitedefou.tp7.b1.crt

# gestion des permissions
sudo chown nginx:nginx /etc/ssl/private/sitedefou.tp7.b1.key
sudo chown nginx:nginx /etc/ssl/certs/sitedefou.tp7.b1.crt
```

![One lock](./img/one_lock.jpg)

➜ **Modifier la configuration NGINX**

- go modifier votre fichier `/etc/nginx/conf.d/sitedefou.tp7.b1.conf`

```nginx
server {
    server_name   sitedefou.tp7.b1;

    # on change le port d'écoute : 443 la convention pour HTTPS
    # il faut aussi ajouter la mention "ssl" pour activer le chiffrement
    listen        <ADRESSE_IP>:443 ssl;

    # et on indique les chemins vers la clé privée et le certificat
    ssl_certificate /etc/ssl/certs/sitedefou.tp7.b1.crt;
    ssl_certificate_key /etc/ssl/private/sitedefou.tp7.b1.key;

    location      / {
        root      /var/www/sitedefou.tp7.b1;
    }
}
```

➜ **Redémarrer le service web NGINX !**

```bash
sudo systemctl restart nginx
```
🌞 **Lister les ports en écoute sur la machine**

- avec une commande adaptée (voir mémo)
```
[root@web ~]# ss -lnpt
State       Recv-Q      Send-Q           Local Address:Port            Peer Address:Port      Process
LISTEN      0           128                    0.0.0.0:22                   0.0.0.0:*          users:(("sshd",pid=692,fd=3))
LISTEN      0           511                    0.0.0.0:80                   0.0.0.0:*          users:(("nginx",pid=1531,fd=7),("nginx",pid=1530,fd=7))
LISTEN      0           511                  10.7.1.11:443                  0.0.0.0:*          users:(("nginx",pid=1531,fd=6),("nginx",pid=1530,fd=6))
LISTEN      0           128                       [::]:22                      [::]:*          users:(("sshd",pid=692,fd=4))
LISTEN      0           511                       [::]:80                      [::]:*          users:(("nginx",pid=1531,fd=8),("nginx",pid=1530,fd=8))
```
- isolez la ligne qui concerne le service NGINX qui écoute en HTTPS
```
[root@web ~]# ss -lnpt |grep 443
LISTEN 0      511        10.7.1.11:443       0.0.0.0:*    users:(("nginx",pid=1531,fd=6),("nginx",pid=1530,fd=6))

```

🌞 **Gérer le firewall**

- ouvrir le nouveau port sur lequel NGINX écoute pour HTTPS
```
[root@web ~]# sudo firewall-cmd --permanent --add-port=443/tcp
success
```
- fermer l'ancien port qui était utilisé pour HTTP
```
[root@web ~]# sudo firewall-cmd --permanent --remove-port=80/tcp
success
```
### B. Test test test analyyyze

> *Sur `client1.tp7.b1`.*

➜ **Tu devrais pouvoir visiter `https://sitedefou.tp7.b1` (bien `https` avec le 's')**

🌞 **Capture `tcp_https.pcap`**
```
vboxuser@client1:~$ sudo tcpdump -w tcp_https.pcap -i enp0s8
```

- **capturer une session TCP complète**
  - le début (SYN, SYN ACK, ACK)
  - des données échangées (PSH, PSH ACK)
    - en l'occurence ce sera votre échange TLS
      - Wireshark affichera "TLS" dans la colonne protocole, mais si vous cliquez dessus, vous verrez que c'est bien contenu dans du TCP PSH
    - puis les données HTTP... chiffrées
  - une fin de session (ça dépend, le plus souvent : FIN, FIN ACK ou RST)
- cette session TCP doit être celle de l'échange HTTPS pour récupérer la page du serveur web
- il faut donc capturer pendant que tu visites le site web en HTTPS

> ⚠️⚠️ *Remarquez que ~~vous pouvez voir en clair le texte "meow !"~~ vous voyez plus rien dukoo dans la capture Wireshark. Juste y'a du trafic chiffré qui circule, c'est tout ce qu'on peut dire.*

![Such secure](./img/such_secure.png)

# III. Serveur VPN

Dans cette partie on va mettre en place un serveur VPN. On va utiliser *Wireguard* : opensource, moderne, robuste, rapide, le feu.

Le but : on se connecte à un serveur VPN en tant que client (là ce sera une VM le serveur VPN, mais à l'autre bout du monde ça a le même effet).

Une fois connecté au VPN, on accès à :

- **on peut désigner le serveur VPN comme passerelle**
  - ainsi, notre traffic ira jusqu'au serveur VPN avant de sortir sur internet
- **un réseau local, à distance**
  - là il contiendra un serveur Web

![VPN](./img/vpn.png)

## Sommaire

- [III. Serveur VPN](#iii-serveur-vpn)
  - [Sommaire](#sommaire)
  - [0. Setup](#0-setup)
    - [A. Schéma](#a-schéma)
    - [B. Tableau adressage](#b-tableau-adressage)
  - [1. Install et conf Wireguard](#1-install-et-conf-wireguard)
  - [2. Ajout d'un client VPN](#2-ajout-dun-client-vpn)
  - [3. Proofs](#3-proofs)
  - [4. Private service](#4-private-service)

## 0. Setup

### A. Schéma

![VPN part](./img/part_vpn.svg)

### B. Tableau adressage

| Machine          | LAN1 `10.7.1.0/24` | LAN2 `10.7.2.0/24` |
|------------------|--------------------|--------------------|
| `client1.tp7.b1` | `10.7.1.101`       | x                  |
| `vpn.tp7.b1`     | `10.7.1.111`       | `10.7.2.111`       |
| `routeur.tp7.b1` | `10.7.1.254`       | x                  |
| `web.tp7.b1`     | x                  | `10.7.2.11`        |

## 1. Install et conf Wireguard

> *Sur `vpn.tp7.b1`.*

➜ **Installer Wireguard**

```bash
sudo dnf install -y epel-release
sudo dnf install -y wireguard-tools
```

➜ **Générer la paire de clés du serveur**

- après cette commande, la clé publique sera stockée dans le fichier `/etc/wireguard/wg0.pub`
- et la clé privée dans `/etc/wireguard/wg0.priv`

```bash
wg genkey | sudo tee /etc/wireguard/wg0.priv | wg pubkey | sudo tee /etc/wireguard/wg0.pub
```

➜ **Ecrire le fichier de configuration du serveur**

- créer le fichier `/etc/wireguard/wg0.conf`
  - il faut remplacer `<CLE_PRIVEE_DU_SERVEUR>` par... la clé privée du serveur
  - `sudo cat /etc/wireguard/wg0.priv` pour l'obtenir

```bash
[Interface]
PrivateKey = <CLE_PRIVEE_DU_SERVEUR>
Address = 10.7.200.1/24
ListenPort = 51820
```

➜ **Démarrez l'interface réseau `wg0`**

```bash
sudo systemctl start wg-quick@wg0
```

🌞 **Prouvez que vous avez bien une nouvelle carte réseau `wg0`**
```
[root@vpn]# ip a
4: wg0: <POINTOPOINT,NOARP,UP,LOWER_UP> mtu 1420 qdisc noqueue state UNKNOWN group default qlen 1000
    link/none
    inet 10.7.200.1/24 scope global wg0
       valid_lft forever preferred_lft forever
```
🌞 **Déterminer sur quel port écoute Wireguard**

```
[root@vpn]# ss -lnpu | grep 51820
UNCONN 0      0            0.0.0.0:51820      0.0.0.0:*
UNCONN 0      0               [::]:51820         [::]:*
```

🌞 **Ouvrez ce port dans le firewall**
``` 
[root@vpn matheo]# sudo firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s8 enp0s9
  sources:
  services: cockpit dhcpv6-client ssh
  ports: 51820/udp
  protocols:
  forward: yes
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:
  ```

➜ **Enfin, activez le routage avec** :

- oublie pas ça, important pour la suite :d

```bash
# on active le forwarding : on accepte de faire passer des paquets
sudo sysctl -w net.ipv4.ip_forward=1
sudo sysctl -w net.ipv6.conf.all.forwarding=1

# on active le masquerading : permettre d'être la passerelle internet des clients
sudo firewall-cmd --add-interface=wg0 --zone=public --permanent
sudo firewall-cmd --add-masquerade --permanent
sudo firewall-cmd --reload
```

## 2. Ajout d'un client VPN

Allez on ajoute un client : il génère ses clés, échange de clé publique, donc fichier de conf qui inclut la clé publique du correspondant.

Z'est bardi.

> *Sur `client1.tp7.b1`.*

➜ **Installer Wireguard**

- allez, je donne pas la commande, demande à google pour savoir comment le faire sur le client !s

➜ **Générer la paire de clés du client**

- même commande que sur le serveur

➜ **Ecrire le fichier de configuration du client**

- créer le fichier `/etc/wireguard/wg0.conf`
  - remplacez les clés par les bonnes valeurs
  - il faut `sudo cat` sur les clés pour les afficher et les insérer dans les configurations
- avec le contenu :

```bash
[Interface]
PrivateKey = <CLE_PRIVEE_DU_CLIENT>
Address = 10.7.200.11/24

[Peer]
PublicKey = <CLE_PUBLIQUE_DU_SERVEUR>
AllowedIPs = 0.0.0.0/0
Endpoint = 10.7.1.111:51820
PersistentKeepalive = 25
```

➜ **Allumer l'interface `wg0` du client**

```bash
sudo wg-quick up wg0
```

---

> *De retour sur le serveur `vpn.tp7.b1`.*

➜ **Modifier le fichier de configuration du serveur**

- on ajoute une section `[Peer]` pour indiquer
  - l'addrese IP du client
  - sa clé publique

```bash
[Interface]
PrivateKey = <CLE_PRIVEE_DU_SERVEUR>
Address = 10.7.200.1/24
ListenPort = 51820


[Peer]
PublicKey = <CLE_PUBLIQUE_DU_CLIENT>
AllowedIps = 10.7.200.11/32
```

➜ **Redémarrer l'interface du serveur après l'ajout du client**

```bash
sudo systemctl restart wg-quick@wg0
```

➜ **Vérifier qu'on voit bien un nouveau client**

```bash
sudo wg show
```

## 3. Proofs

🌞 **Ping ping ping !**

```
vboxuser@client1:~$ ping 10.7.200.1
PING 10.7.200.1 (10.7.200.1) 56(84) bytes of data.
64 bytes from 10.7.200.1: icmp_seq=1 ttl=64 time=0.601 ms
64 bytes from 10.7.200.1: icmp_seq=2 ttl=64 time=0.618 ms
64 bytes from 10.7.200.1: icmp_seq=3 ttl=64 time=1.61 ms
^C
--- 10.7.200.1 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2050ms
rtt min/avg/max/mdev = 0.601/0.941/1.606/0.469 ms```

```

🌞 **Capture `ping1_vpn.pcap`**

```
vboxuser@client1:~$ sudo tcpdump -w ping1_vpn.pcap -i enp0s8
```

🌞 **Capture `ping2_vpn.pcap`**

```
vboxuser@client1:~$ sudo tcpdump -w ping2_vpn.pcap -i enp0s8

```

➜ **Sur le [`client1.tp7.b1`**](`client1.tp7.b1`**.md)

- ajoutez `10.7.200.1` comme votre passerelle, temporairement :

```bash
# on supprime la passerelle actuelle manuellement
sudo ip route delete default

# on ajoute une passerelle qui passe par le serveur VPN
sudo ip route add default via 10.7.200.1
```

🌞 **Prouvez que vous avez toujours un accès internet**

``` 
root@client1:# ip route
default via 10.7.200.1 dev wg0
10.7.1.0/24 dev enp0s8 proto kernel scope link src 10.7.1.101 metric 100
10.7.200.0/24 dev wg0 proto kernel scope link src 10.7.200.11
```

> On devrait voir que pour aller sur internet, vous passez par le serveur VPN.

## 4. Private service

> *Sur `web.tp7.b1`.*

Last but not least, le serveur Web. Il est isolé dans un LAN (potentiellement à l'autre bout du monde), impossible d'y accéder... sauf si on est connectés au VPN.

Pour ce faire :

- recycler la machine web de la partie d'avant
- branchez-le au nouveau host-only
- attribuez-lui la nouvelle IP `10.7.2.11`
- installer wireguard, et ajouter la machine comme nouveau client (même manip que sur `client1.tp7.b1`)
  - l'IP de `web.tp7.b1` doit être `10.7.200.37`
- modifier la configuration NGINX pour qu'il écoute uniquement sur l'IP `10.7.200.37`

🌞 **Visitez le service Web à travers le VPN**

```
vboxuser@client1:~$ curl https://sitedefou.tp7.b1 -k
meow !
```
