# I. Récolte d'informations 
## 🌞 Adresses IP de ta machine
### affiche l'adresse IP que ta machine a sur sa carte réseau WiFi

*commande: ipconfig /all*
```
 PS C:\Users\rodne> ipconfig
Carte réseau sans fil Wi-Fi :

   Suffixe DNS propre à la connexion. . . :
   Adresse IPv6 de liaison locale. . . . .: fe80::3d1c:c538:f0bf:62e9%14
   Adresse IPv4. . . . . . . . . . . . . .: 10.33.73.134
   Masque de sous-réseau. . . . . . . . . : 255.255.240.0
   Passerelle par défaut. . . . . . . . . : 10.33.79.254
```
### affiche l'adresse IP que ta machine a sur sa carte réseau ethernet
```none
```
## 🌞 Si t'as un accès internet normal, d'autres infos sont forcément dispos...
### affiche l'adresse IP de la passerelle du réseau local

*commande: ipconfig /all*
```
Carte réseau sans fil Wi-Fi :

   Suffixe DNS propre à la connexion. . . :
   Description. . . . . . . . . . . . . . : Realtek 8821CE Wireless LAN 802.11ac PCI-E NIC
   Adresse physique . . . . . . . . . . . : 2C-3B-70-73-68-0B
   DHCP activé. . . . . . . . . . . . . . : Oui
   Configuration automatique activée. . . : Oui
   Adresse IPv6 de liaison locale. . . . .: fe80::3d1c:c538:f0bf:62e9%14(préféré)
   Adresse IPv4. . . . . . . . . . . . . .: 10.33.73.134(préféré)
   Masque de sous-réseau. . . . . . . . . : 255.255.240.0
   Bail obtenu. . . . . . . . . . . . . . : vendredi 27 septembre 2024 09:05:03
   Bail expirant. . . . . . . . . . . . . : samedi 28 septembre 2024 08:10:42
   Passerelle par défaut. . . . . . . . . : 10.33.79.254
   Serveur DHCP . . . . . . . . . . . . . : 10.33.79.254
   IAID DHCPv6 . . . . . . . . . . . : 120339312
   DUID de client DHCPv6. . . . . . . . : 00-01-00-01-2B-91-3C-7F-2C-3B-70-73-68-0B
   Serveurs DNS. . .  . . . . . . . . . . : 8.8.8.8
                                       1.1.1.1
   NetBIOS sur Tcpip. . . . . . . . . . . : Activé 
   ```
   ### affiche l'adresse IP du serveur DNS que connaît ton PC

  *commande: ipconfig /all*

  ```
   Carte réseau sans fil Wi-Fi :

   Suffixe DNS propre à la connexion. . . :
   Description. . . . . . . . . . . . . . : Realtek 8821CE Wireless LAN 802.11ac PCI-E NIC
   Adresse physique . . . . . . . . . . . : 2C-3B-70-73-68-0B
   DHCP activé. . . . . . . . . . . . . . : Oui
   Configuration automatique activée. . . : Oui
   Adresse IPv6 de liaison locale. . . . .: fe80::3d1c:c538:f0bf:62e9%14(préféré)
   Adresse IPv4. . . . . . . . . . . . . .: 10.33.73.134(préféré)
   Masque de sous-réseau. . . . . . . . . : 255.255.240.0
   Bail obtenu. . . . . . . . . . . . . . : vendredi 27 septembre 2024 09:05:03
   Bail expirant. . . . . . . . . . . . . : samedi 28 septembre 2024 08:10:42
   Passerelle par défaut. . . . . . . . . : 10.33.79.254
   Serveur DHCP . . . . . . . . . . . . . : 10.33.79.254
   IAID DHCPv6 . . . . . . . . . . . : 120339312
   DUID de client DHCPv6. . . . . . . . : 00-01-00-01-2B-91-3C-7F-2C-3B-70-73-68-0B
   Serveurs DNS. . .  . . . . . . . . . . : 8.8.8.8
                                       1.1.1.1
   NetBIOS sur Tcpip. . . . . . . . . . . : Activé
   ```
  ### affiche l'adresse IP du serveur DHCP que connaît ton PC 

  *commande: ipconfig /all*
  ```
  Carte réseau sans fil Wi-Fi :

   Suffixe DNS propre à la connexion. . . :
   Description. . . . . . . . . . . . . . : Realtek 8821CE Wireless LAN 802.11ac PCI-E NIC
   Adresse physique . . . . . . . . . . . : 2C-3B-70-73-68-0B
   DHCP activé. . . . . . . . . . . . . . : Oui
   Configuration automatique activée. . . : Oui
   Adresse IPv6 de liaison locale. . . . .: fe80::3d1c:c538:f0bf:62e9%14(préféré)
   Adresse IPv4. . . . . . . . . . . . . .: 10.33.73.134(préféré)
   Masque de sous-réseau. . . . . . . . . : 255.255.240.0
   Bail obtenu. . . . . . . . . . . . . . : vendredi 27 septembre 2024 09:05:03
   Bail expirant. . . . . . . . . . . . . : samedi 28 septembre 2024 08:10:42
   Passerelle par défaut. . . . . . . . . : 10.33.79.254
   Serveur DHCP . . . . . . . . . . . . . : 10.33.79.254
   IAID DHCPv6 . . . . . . . . . . . : 120339312
   DUID de client DHCPv6. . . . . . . . : 00-01-00-01-2B-91-3C-7F-2C-3B-70-73-68-0B
   Serveurs DNS. . .  . . . . . . . . . . : 8.8.8.8
                                       1.1.1.1
   NetBIOS sur Tcpip. . . . . . . . . . . : Activé 
   ```
## 🌟 BONUS : Détermine s'il y a un pare-feu actif sur ta machine
*commande:  Get-NetFirewallProfile | Format-Table -Property Name, Enabled*
   ```
  Name    Enabled
----    -------
Domain     True
Private    True
Public     True

```
# II. Utiliser le réseau
## 🌞 Envoie un ping vers...
### toi-même !
*commande: ping 192.168.1.77*
```
Envoi d’une requête 'Ping'  192.168.1.77 avec 32 octets de données :
Réponse de 192.168.1.77 : octets=32 temps<1ms TTL=128
Réponse de 192.168.1.77 : octets=32 temps<1ms TTL=128
Réponse de 192.168.1.77 : octets=32 temps<1ms TTL=128
Réponse de 192.168.1.77 : octets=32 temps<1ms TTL=128

Statistiques Ping pour 192.168.1.77:
    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 0ms, Maximum = 0ms, Moyenne = 0ms
```    
### vers l'adresse IP 127.0.0.1
*commande: ping 127.0.0.1*
```
Envoi d’une requête 'Ping'  127.0.0.1 avec 32 octets de données :
Réponse de 127.0.0.1 : octets=32 temps<1ms TTL=128
Réponse de 127.0.0.1 : octets=32 temps<1ms TTL=128
Réponse de 127.0.0.1 : octets=32 temps<1ms TTL=128
Réponse de 127.0.0.1 : octets=32 temps<1ms TTL=128

Statistiques Ping pour 127.0.0.1:
    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 0ms, Maximum = 0ms, Moyenne = 0ms 
    
````

## 🌞 On continue avec ping. Envoie un ping vers...
### ta passerelle...
*commande: ping 192.168.1.1* 
```

Envoi d’une requête 'Ping'  192.168.1.1 avec 32 octets de données :
Réponse de 192.168.1.1 : octets=32 temps=255 ms TTL=64
Réponse de 192.168.1.1 : octets=32 temps=130 ms TTL=64
Réponse de 192.168.1.1 : octets=32 temps=34 ms TTL=64
Réponse de 192.168.1.1 : octets=32 temps=297 ms TTL=64

Statistiques Ping pour 192.168.1.1:
    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 34ms, Maximum = 297ms, Moyenne = 179ms

````    
### un(e) pote sur le réseau
*commande: ping 192.168.1.29*
```` 
Envoi d’une requête 'Ping'  192.168.1.29 avec 32 octets de données :
Réponse de 192.168.1.29 : octets=32 temps=632 ms TTL=64
Réponse de 192.168.1.29 : octets=32 temps=456 ms TTL=64
Réponse de 192.168.1.29 : octets=32 temps=302 ms TTL=64
Réponse de 192.168.1.29 : octets=32 temps=210 ms TTL=64

Statistiques Ping pour 192.168.1.29:
    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 210ms, Maximum = 632ms, Moyenne = 400ms
```` 
## un site internet

commande: ping Google.com*
``` 
Envoi d’une requête 'ping' sur Google.com [172.217.18.206] avec 32 octets de données :
Réponse de 172.217.18.206 : octets=32 temps=19 ms TTL=117
Réponse de 172.217.18.206 : octets=32 temps=30 ms TTL=117
Réponse de 172.217.18.206 : octets=32 temps=19 ms TTL=117
Réponse de 172.217.18.206 : octets=32 temps=21 ms TTL=117

Statistiques Ping pour 172.217.18.206:
    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 19ms, Maximum = 30ms, Moyenne = 22ms
```
## 🌞 Faire une requête DNS à la main
### www.thinkerview.com
*commande: nslookup  www.thinkerview.com

```Serveur :   dns.google
Address:  8.8.8.8

Réponse ne faisant pas autorité :
Nom :    www.thinkerview.com
Addresses:  2a06:98c1:3121::7
          2a06:98c1:3120::7
          188.114.96.7
          188.114.97.7
```
###  www.wikileaks.org
*commande: nslookup www.wikileaks.org
```Serveur :   dns.google
Address:  8.8.8.8

Réponse ne faisant pas autorité :
Nom :    wikileaks.org
Addresses:  80.81.248.21
          51.159.197.136
Aliases:  www.wikileaks.org
```
### www.torproject.org
*commande: nslookup www.torproject.org *
``` 
Réponse ne faisant pas autorité :
Nom :    www.torproject.org
Addresses:  2a01:4f8:fff0:4f:266:37ff:feae:3bbc
          2a01:4f9:c010:19eb::1
          2a01:4f8:fff0:4f:266:37ff:fe2c:5d19
          2620:7:6002:0:466:39ff:fe32:e3dd
          2620:7:6002:0:466:39ff:fe7f:1826
          204.8.99.146
          204.8.99.144
          116.202.120.165
          116.202.120.166
          95.216.163.36
``` 
# IV. Network scanning et adresses IP

## 🌞 Effectue un scan du réseau auquel tu es connecté

*commande: PS C:\Users\rodne> nmap -sn -PR 192.168.1.1/24*
```Starting Nmap 7.95 ( https://nmap.org ) at 2024-10-06 14:17 Paris, Madrid (heure dÆÚtÚ)
Nmap scan report for box (192.168.1.1)
Host is up (0.0039s latency).
MAC Address: CC:2D:1B:79:20:48 (SFR)
Nmap scan report for M2007J3SG (192.168.1.28)
Host is up (0.20s latency).
MAC Address: 34:1C:F0:F8:A4:90 (Xiaomi Communications)
Nmap scan report for 192.168.1.36
Host is up (0.28s latency).
MAC Address: 3A:CD:A7:E2:5A:7A (Unknown)
Nmap scan report for Mi-11-Lite-5G (192.168.1.58)
Host is up (0.14s latency).
MAC Address: E6:10:DD:B6:05:AA (Unknown)
Nmap scan report for bosch-dishwasher-403110520721054632 (192.168.1.67)
Host is up (0.19s latency).
MAC Address: C8:D7:78:B4:DB:F8 (BSH Hausgeraete GmbH)
Nmap scan report for S24-Ultra-de-Mark (192.168.1.73)
Host is up (0.27s latency).
MAC Address: 8A:7C:2B:E7:F3:97 (Unknown)
```
 ## 🌞 Changer d'adresse IP

adresse ip choisi :192.168.1.60

*commande : New-NetIPAddress -InterfaceAlias "Wi-Fi" -IPAddress 192.168.1.60 -PrefixLength 24 -DefaultGateway 192.168.1.1*
```

Carte réseau sans fil Wi-Fi :

   Suffixe DNS propre à la connexion. . . :
   Adresse IPv6. . . . . . . . . . . . . .: 2a02:8428:e04:a601:eb72:b6f0:5bc5:a31a
   Adresse IPv6 temporaire . . . . . . . .: 2a02:8428:e04:a601:5d45:cb55:da26:e1fb
   Adresse IPv6 de liaison locale. . . . .: fe80::3d1c:c538:f0bf:62e9%14
   Adresse IPv4. . . . . . . . . . . . . .: 192.168.1.60
   Masque de sous-réseau. . . . . . . . . : 255.255.255.0
   Passerelle par défaut. . . . . . . . . : fe80::ce2d:1bff:fe79:2048%14
                                       192.168.1.1
```

