# TP2 : Hey yo tell a neighbor tell a friend :
## I. Simplest LAN 
### **0. Setup** 
>Configurez vos adresses IP
```powershell
PS C:\WINDOWS\system32> New-NetIPAddress -InterfaceAlias "Ethernet" -IPAddress 10.10.13.20 -PrefixLength 24


IPAddress         : 10.10.13.20
InterfaceIndex    : 22
InterfaceAlias    : Ethernet
AddressFamily     : IPv4
Type              : Unicast
PrefixLength      : 24
PrefixOrigin      : Manual
SuffixOrigin      : Manual
AddressState      : Tentative
ValidLifetime     : Infinite ([TimeSpan]::MaxValue)
PreferredLifetime : Infinite ([TimeSpan]::MaxValue)
SkipAsSource      : False
PolicyStore       : ActiveStore
```

### **1. Quelques pings**
## 🌞 Prouvez que votre configuration est effective
```powershell
PS C:\WINDOWS\system32> Get-NetIPAddress -InterfaceAlias "Ethernet"


IPAddress         : 10.10.13.20
InterfaceIndex    : 22
InterfaceAlias    : Ethernet
AddressFamily     : IPv4
Type              : Unicast
PrefixLength      : 24
PrefixOrigin      : Manual
SuffixOrigin      : Manual
AddressState      : Preferred
ValidLifetime     : Infinite ([TimeSpan]::MaxValue)
PreferredLifetime : Infinite ([TimeSpan]::MaxValue)
SkipAsSource      : False
PolicyStore       : ActiveStore
```
## 🌞 Tester que votre LAN + votre adressage IP est fonctionnel
## 🌞 Capture de ping
> Dans le fichier "ping.pcapng"
## II. Utilisation des ports
### **0. Intro blabla**
### **1. Animal numérique**
## 🌞 Sur le PC serveur
```powersell
PS C:\Users\mathe\Downloads\netcat-win32-1.11\netcat-1.11> ./nc.exe -l -p 1234
```
## 🌞 Sur le PC client
```powershell
PS C:\Users\mathe\Downloads\netcat-win32-1.11\netcat-1.11> ./nc.exe 10.10.13.20 1234
```
## 🌞 Echangez-vous des messages
```powershell
PS C:\Users\mathe\Downloads\netcat-win32-1.11\netcat-1.11> ./nc.exe -l -p 1234
yo
quoi

feur
ça marche
```
## 🌞 Utilisez une commande qui permet de voir la connexion en cours
```powershell
PS C:\WINDOWS\system32> netstat -a -b -n

 TCP    10.10.13.20:1234       10.10.13.15:39962      ESTABLISHED
 [nc.exe]
 ```

 # 🌞 Inversez les rôles
 
 ## 🌞 Sur le PC serveur
```powersell
PS C:\Users\mathe\Downloads\netcat-win32-1.11\netcat-1.11> ./nc.exe -l -p 1234
```
## 🌞 Sur le PC client
```powershell
PS C:\Users\mathe\Downloads\netcat-win32-1.11\netcat-1.11> ./nc.exe 10.10.13.15 1234
```
```powershell
PS C:\Users\mathe\Downloads\netcat-win32-1.11\netcat-1.11> ./nc.exe 10.10.13.15 1234
salut
je suis ton pere
BYBEYEBYEBEYBE
```
## III. Analyse de vos applications usuelles
### **1. Serveur web**
