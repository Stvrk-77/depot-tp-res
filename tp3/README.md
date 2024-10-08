# TP3 : 32°13'34"N 95°03'27"W

Un TP3 plutôt court dédié au protocole ARP.

![ARP ARP ARP](./img/arparparp.jpg)

## Sommaire

- [TP3 : 32°13'34"N 95°03'27"W](#tp3--321334n-950327w)
  - [Sommaire](#sommaire)
- [0. Prérequis](#0-prérequis)
- [I. ARP basics](#i-arp-basics)
- [II. ARP dans un réseau local](#ii-arp-dans-un-réseau-local)
  - [1. Basics](#1-basics)
  - [2. ARP](#2-arp)
  - [3. Bonus : ARP poisoning](#3-bonus--arp-poisoning)

# 0. Prérequis

- **tout se fait en ligne de commande**, sauf si c'est explicitement précisément autrement
- je tente un truc risqué, ceux du matin, vous servez de crash test
  - **vous allez utiliser vos partages de co dans la partie II**
  - **partie II qui se fait à 3 ou 4** (pas + SVP, c'est déjà le cirque à 4)
  - si ça fonctionne pas ce sera backup plan avec les câbles :c

# I. ARP basics

> ⚠️ **Pas besoin du partage de co pour le moment, restez sur le réseau de l'école en WiFi.**

☀️ **Avant de continuer...**

- affichez l'adresse MAC de votre carte WiFi !


```PS C:\Users\rodne> ipconfig /all
PS C:\Users\rodne> ipconfig /all

Carte réseau sans fil Wi-Fi :

   Suffixe DNS propre à la connexion. . . :
   Description. . . . . . . . . . . . . . : Realtek 8821CE Wireless LAN 802.11ac PCI-E NIC
   Adresse physique . . . . . . . . . . . : 2C-3B-70-73-68-0B
   DHCP activé. . . . . . . . . . . . . . : Oui
   Configuration automatique activée. . . : Oui
   Adresse IPv6 de liaison locale. . . . .: fe80::3d1c:c538:f0bf:62e9%14(préféré)
   Adresse IPv4. . . . . . . . . . . . . .: 10.33.73.134(préféré)
   Masque de sous-réseau. . . . . . . . . : 255.255.240.0
   Bail obtenu. . . . . . . . . . . . . . : mardi 8 octobre 2024 09:21:13
   Bail expirant. . . . . . . . . . . . . : mercredi 9 octobre 2024 08:32:19
   Passerelle par défaut. . . . . . . . . : 10.33.79.254
   Serveur DHCP . . . . . . . . . . . . . : 10.33.79.254
   IAID DHCPv6 . . . . . . . . . . . : 120339312
   DUID de client DHCPv6. . . . . . . . : 00-01-00-01-2B-91-3C-7F-2C-3B-70-73-68-0B
   Serveurs DNS. . .  . . . . . . . . . . : 8.8.8.8
                                       1.1.1.1
````                                   

☀️ **Affichez votre table ARP**

 ```PS C:\Users\rodne> arp -a
 PS C:\Users\rodne> arp -a

Interface : 192.168.56.1 --- 0xa
  Adresse Internet      Adresse physique      Type
  192.168.56.255        ff-ff-ff-ff-ff-ff     statique
  224.0.0.2             01-00-5e-00-00-02     statique
  224.0.0.22            01-00-5e-00-00-16     statique
  224.0.0.251           01-00-5e-00-00-fb     statique
  224.0.0.252           01-00-5e-00-00-fc     statique
  224.76.78.75          01-00-5e-4c-4e-4b     statique
  239.255.132.178       01-00-5e-7f-84-b2     statique
  239.255.255.250       01-00-5e-7f-ff-fa     statique
  255.255.255.255       ff-ff-ff-ff-ff-ff     statique

Interface : 10.33.73.134 --- 0xe
  Adresse Internet      Adresse physique      Type
  10.33.65.63           44-af-28-c3-6a-9f     dynamique
  10.33.73.77           98-8d-46-c4-fa-e5     dynamique
  10.33.77.160          c8-94-02-f8-ab-97     dynamique
  10.33.79.254          7c-5a-1c-d3-d8-76     dynamique
  10.33.79.255          ff-ff-ff-ff-ff-ff     statique
  224.0.0.2             01-00-5e-00-00-02     statique
  224.0.0.22            01-00-5e-00-00-16     statique
  224.0.0.251           01-00-5e-00-00-fb     statique
  224.0.0.252           01-00-5e-00-00-fc     statique
  224.76.78.75          01-00-5e-4c-4e-4b     statique
  239.255.132.178       01-00-5e-7f-84-b2     statique
  239.255.255.250       01-00-5e-7f-ff-fa     statique
  255.255.255.255       ff-ff-ff-ff-ff-ff     statique
```

☀️ **Déterminez l'adresse MAC de la passerelle du réseau de l'école**
``` 
10.33.79.254          7c-5a-1c-d3-d8-76     dynamique
``` 

☀️ **Supprimez la ligne qui concerne la passerelle**
```
PS C:\Users\rodne> arp -d 10.33.79.254
``` 
☀️ **Prouvez que vous avez supprimé la ligne dans la table ARP**
```
PS C:\Users\rodne> arp -a

Interface : 192.168.56.1 --- 0xa
  Adresse Internet      Adresse physique      Type
  192.168.56.255        ff-ff-ff-ff-ff-ff     statique
  224.0.0.2             01-00-5e-00-00-02     statique
  224.0.0.22            01-00-5e-00-00-16     statique
  224.0.0.251           01-00-5e-00-00-fb     statique
  224.0.0.252           01-00-5e-00-00-fc     statique
  224.76.78.75          01-00-5e-4c-4e-4b     statique
  239.255.132.178       01-00-5e-7f-84-b2     statique
  239.255.255.250       01-00-5e-7f-ff-fa     statique
  255.255.255.255       ff-ff-ff-ff-ff-ff     statique
```  
☀️ **Wireshark**

- capture `arp1.pcap`
# II. ARP dans un réseau local

> ⚠️ **Avant de continuer, regroupez-vous par 3 ou 4, et connectez-vous au même partage de co avec le téléphone de l'un d'entre vous.** Si ça fonctionne pas, vous pouvez faire cette partie sur le réseau de l'école mais moins bieng.

Dans cette situation, le téléphone agit comme une "box" :

- **c'est un switch**
  - il permet à tout le monde d'être connecté à un même réseau local
- **c'est aussi un routeur**
  - il fait passer les paquets du réseau local, à internet
  - et vice-versa
  - on dit que ce routeur est la **passerelle** du réseau local

## 1. Basics

> ⚠️ **Avant de continuer, regroupez-vous par 3 ou 4, et connectez-vous au même partage de co avec le téléphone de l'un d'entre vous.** Si ça fonctionne pas, vous pouvez faire cette partie sur le réseau de l'école mais moins bieng.

☀️ **Déterminer**
```
PS C:\Users\rodne> ipconfig /all

Carte réseau sans fil Wi-Fi :

   Suffixe DNS propre à la connexion. . . :
   Description. . . . . . . . . . . . . . : Intel(R) Wi-Fi 6 AX201 160MHz
   Adresse physique . . . . . . . . . . . : FC-B3-BC-C7-28-6A
   DHCP activé. . . . . . . . . . . . . . : Oui
   Configuration automatique activée. . . : Oui
   Adresse IPv4. . . . . . . . . . . . . .: 192.168.180.196(préféré)
   Masque de sous-réseau. . . . . . . . . : 255.255.255.0
   Bail obtenu. . . . . . . . . . . . . . : Tuesday, October 8, 2024 10:51:42 AM
   Bail expirant. . . . . . . . . . . . . : Tuesday, October 8, 2024 11:55:20 AM
   Passerelle par défaut. . . . . . . . . : 192.168.180.29
   Serveur DHCP . . . . . . . . . . . . . : 192.168.180.29
   Serveurs DNS. . .  . . . . . . . . . . : 192.168.180.29
```
☀️ **DIY**
```
PS C:\Users\rodne> ipconfig /all

Carte réseau sans fil Wi-Fi :

   Suffixe DNS propre à la connexion. . . :
   Description. . . . . . . . . . . . . . : Intel(R) Wi-Fi 6 AX201 160MHz
   Adresse physique . . . . . . . . . . . : FC-B3-BC-C7-28-6A
   DHCP activé. . . . . . . . . . . . . . : Non
   Configuration automatique activée. . . : Oui
   Adresse IPv4. . . . . . . . . . . . . .: 192.168.180.197(préféré)
   Masque de sous-réseau. . . . . . . . . : 255.255.255.0
   Passerelle par défaut. . . . . . . . . : 192.168.180.29
   Serveurs DNS. . .  . . . . . . . . . . : 192.168.180.29
   NetBIOS sur Tcpip. . . . . . . . . . . : Activ
   ```

☀️ **Pingz !**
```
PS C:\Users\rodne> ping Google.com

Envoi d’une requête 'ping' sur Google.com [216.58.214.78] avec 32 octets de données :
Réponse de 216.58.214.78 : octets=32 temps=16 ms TTL=117
Réponse de 216.58.214.78 : octets=32 temps=17 ms TTL=117
Réponse de 216.58.214.78 : octets=32 temps=17 ms TTL=117
Réponse de 216.58.214.78 : octets=32 temps=17 ms TTL=117
```

## 2. ARP

☀️ **Affichez votre table ARP !**
```
PS C:\Users\rodne> arp -a
Interface : 169.254.63.157 --- 0x8
  Adresse Internet      Adresse physique      Type
  169.254.255.255       ff-ff-ff-ff-ff-ff     statique
  224.0.0.22            01-00-5e-00-00-16     statique
  224.0.0.251           01-00-5e-00-00-fb     statique
  224.0.0.252           01-00-5e-00-00-fc     statique
  239.255.255.250       01-00-5e-7f-ff-fa     statique
  255.255.255.255       ff-ff-ff-ff-ff-ff     statique

Interface : 192.168.180.197 --- 0x18
  Adresse Internet      Adresse physique      Type
  192.168.180.29        e2-b6-7a-88-24-14     dynamique
  192.168.180.255       ff-ff-ff-ff-ff-ff     statique
  224.0.0.22            01-00-5e-00-00-16     statique
  224.0.0.251           01-00-5e-00-00-fb     statique
  224.0.0.252           01-00-5e-00-00-fc     statique
  239.255.255.250       01-00-5e-7f-ff-fa     statique
  255.255.255.255       ff-ff-ff-ff-ff-ff     statique
```

➜ **Wireshark that**



☀️ **Capture arp2.pcap**

## 3. Bonus : ARP poisoning

![ARP poisoning](./img/ARP_poisoning.svg)

> ⚠️⚠️⚠️ Si le partage de co n'a pas fonctionné, **il est hors de question que vous fassiez ça sur le réseau de l'école. Donc partage de co ou avec des VMs.**

⭐ **Empoisonner la table ARP de l'un des membres de votre réseau**

- il faut donc forcer l'injection de fausses informations dans la table ARP de quelqu'un d'autre
- on peut le faire en envoyant des messages ARP que l'on a forgé nous-mêmes
- avec quelques lignes de code, ou y'a déjà ce qu'il faut sur internet
- faites vos recherches, demandez-moi si vous voulez de l'aide
- affichez la table ARP de la victime une fois modifiée dans le compte-rendu

⭐ **Mettre en place un MITM**

- MITM pour Man-in-the-middle
- placez vous entre l'un des membres du réseau, et la passerelle
- ainsi, dès que ce PC va sur internet, c'est à vous qu'il envoie tous ses messages
- pour ça, il faut continuellement empoisonner la table ARP de la victime, et celle de la passerelle

> Au moins tous ceux veulent faire de la sécu, il faudrait faire cette partie bonus ! Prenez-vous un peu la tête pour le réaliser, avec des VMs y'aura ptet moins de trucs dans tous les sens pour une première fois. Hésitez pas à m'appeler.

![ARP sniff](./img/arp.jpg)

