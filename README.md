# Cisco Packet Tracer Cheat Sheet
Informationen zu Konfigurationen mit dem Cisco Packet Tracer

## Router 
### Enable Bereich betreten
Um in den privilegierten Modus eines Routers zu kommen wird folgender Befehl eingegeben: `enable`
Ist ein Passwort gesetzt, muss dieses anschließend eingegeben werden, ansonsten ist man schon im privilegierten Modus.
### Konfigurations-Modus aufrufen
Der Befehl für den Konfigurations Modus lautet: `configure terminal`
In diesem Modus kann man bspw. ein Interface konfigurieren.
Hinweis: Einige Befehle wie `show run` benötigen in diesem Modus ein `do` vor dem Befehl.
### Enable Passwort setzen
Ein wichtiger Schritt bei der Konfiguration eines Routers ist das Setzen eines Passworts. Dieses muss dann eingegeben werden, um den privilegierten Modus zu betreten. Um mehr Sicherheit zu haben, muss man das Passwort verschlüsselt speichern. Dazu wird dieser Befehl im Konfigurationsmodus verwendet: `enable secret PASSWORT` Bei den Cisco Übungen wird als Passwort `cisco` verwendet.
### Hostname setzen
Um nicht den Standardhostname zu haben, gibt es die Möglichkeit einen eigenen zu setzen. Im Konfig-Mode wird dazu dieser Befehl verwendet: `hostname HOSTNAME`. Als Hostname kann man bspw. `R0` nehmen.
### Write 
Um die eingestellten Daten in den Speicher in den persistenten Speicher zu schreiben, gibt es den Befehl `write` (im im privilegierten Modus) bzw. `do write` (Konfigurationsmodus).
### Reload
Um den Router neuzustarten gibt es `reload` Privilegierter Modus, bzw. `do reload`.
### IP Adresse bei einem Port zuweisen
Um einem Port (z.B. GigabyteEthernet 0/0) eine IP Adresse zuzuweisen wird folgendes gemacht:
```
R0(config)#interface GigabitEthernet 0/0
R0(config-if)#ip address 10.0.0.1 255.0.0.0
R0(config-if)#no shutdown 
R0(config-if)#exit
```

### SSH Einrichten
```
Router>enable
Router#configure terminal 
Router(config)#hostname R0
R0(config)#interface GigabitEthernet 0/0
R0(config-if)#ip address 10.0.0.1 255.0.0.0
R0(config-if)#no shutdown 
R0(config-if)#exit
R0(config)#line vty 0 15
R0(config-line)#password cisco
R0(config-line)#login
R0(config-line)#transport input ssh
R0(config-line)#exit
Router(config)#ip domain-name git-cvfz.com
R0(config)#crypto key generate rsa general-keys modulus 4096
R0(config)#interface vlan1 
R0(config-if)#exit
```
Mit diesem Code wurde ein Virtueller Konsolen-Port am Router 0 angelegt, dort kann man per SSH zugreifen.

### DHCP Pool einrichten
Folgend sind die Befehle, um einen Router als DHCP Server zu verwenden.
```
Router>enable 
Router#configure terminal 

Router(config)#interface GigabitEthernet 0/0
Router(config-if)#ip address 10.20.30.1 255.255.255.0
Router(config-if)#no shutdown 
Router(config-if)#exit

Router(config)#service dhcp
Router(config)#ip dhcp pool CVFZ
Router(dhcp-config)#domain-name git-cvfz.com
Router(dhcp-config)#dns 8.8.8.8
Router(dhcp-config)#network 10.20.30.0 255.255.255.0
Router(dhcp-config)#default-router 10.20.30.1
Router(dhcp-config)#exit
Router(config)#ip dhcp excluded-address 10.20.30.1 10.20.30.10
```

### Routing
Um von einem Router ein Packet zu einem anderen Router leiten zu können, wird Routing verwendet. Es gibt dabei sowohl _static route_ als auch _dynamic route_. Ersteres ist eine Statische Route, d.h. genau auf __einen__ Pfad, bei zweiterem kann das Paket über mehrere Wege zum Ziel kommen. Zum Konfigurieren wird der Befehl ``ip route NETZWERK_IP SUBNET_MASK ZIEL_ROUTER_IP`` verwendet. -> bspw. ``ip route 10.100.100.0 255.255.255.0 10.100.100.1`` 
Um auf mehrere Netzwerke über einem Router zugreifen zu können, muss man als Netzwerk ``0.0.0.0`` mit der Subnetmask ``0.0.0.0`` verwenden.


### Subinterface
Wenn man VLANs mit einem Router verbinden möchte, verwendet man Subinterfaces verwenden. 
Dazu wird der Befehl `interface GigabitEthernet 0/x.VLAN_NUMBER` verwendet. Anschließend wird die IP Adresse mit Subnetmaske gesetzt. Dann muss noch `encapsulation dot1Q 10` eingegeben werden.

---


## Switch
### Enable -> Reload
Diese Befehle können 1:1 von der Router Konfiguration übernommen werden.

### IP Adresse für VLAN
Um einem VLAN eine IP Adresse zuzuweisen wird folgendes gemacht:
```
SW1(config)#int vlan1
SW1(config-if)#ip address 10.20.30.1 255.255.255.0
SW1(config-if)#no shutdown 
SW1(config-if)#exit
```

### SSH
```
Switch>enable 
Switch#configure terminal
Switch(config)#hostname SW1

SW1(config)#int vlan1
SW1(config-if)#ip address 10.20.30.1 255.255.255.0
SW1(config-if)#no shutdown 
SW1(config-if)#exit

SW1(config)#line vty 0 15
SW1(config-line)#transport input ssh
SW1(config-line)#password cisco
SW1(config-line)#login
SW1(config-line)#exit

SW1(config)#ip domain-name git-cvfz.com
SW1(config)#crypto key generate rsa general-keys modulus 4096
```

### VALN erstellen / Namen setzen
Um ein VLAN zu erstellen muss lediglich im Config Mode `vlan NUMBER` und anschließend `name NAME` eingegeben werden. Mit `exit` kommt man dann wieder zurück.

### Interface Range
Wenn man bspw. VLANs erstellt empfiehlt es sich, Interface Range zu verwenden, um mehrere Ports auszuwählen. Damit ist man schneller fertig, als wenn man jeden Port einzeln konfiguriert. Dazu wird der Befehl `interface range ETHERNET_TYPE 0/Start-End` verwendet. Beispielsweise lautet dieser Befehl `interface range FastEthernet 0/1-12`.

### Trunk Ports
Will man zwischen mehreren Switches über VLANs kommunizieren werden Trunk Ports verwendet. Nach Meinung eines Experten kommt die Bezeichnung "Trunk" vom englischen "drunk" - also betrunken - also der Port ist betrunken, und es kümmert ihm nicht, welche VLANs über ihn kommunizieren. 
Um den Port zu setzen wird zuerst ein Port ausgewählt (also `interface ...`) und anschließend `switchport mode trunk` eingegeben. Um dann noch die VLANs zu erlauben, müssen die entsprechenden ausgewählt werden. Dazu wird `switchport trunk vlan VLAN_Number1,VLAN_Number2,...` verwendet.

### Access Ports
Access Ports sind Ports, an denen normale Geräte kommunizieren können. Konfiguration ähnlich zu den Trunk Ports. `switchport mode access` und `switchport access vlan VLAN`.

### NAT / PAT
Zuerst wählt man das Interface aus (`interface Giga/Fast x/x/x`). Dann kann man mit dem Befehl `ip nat outside` bzw. `ip nat inside` die Richtige Seite einstellen. Dann muss noch eine access list erstellt werden. Dazu verwendet man `ip access-list standard PAT_ACL` (PAT_ACL = Name des Interface). Dann können die IPs erlaubt werden. Das geht mit `permit *IP Adresse* *Wildcard*`. Die Wildcard ist eine invertierte Subnetmaske. Bspw. 255.255.224.0 == 0.0.31.255. 
Dann dieses wieder verlassen und `ip nat inside source list PAT_ACL interface GigabitEthernet 0/0/0 overload` (hier muss der Name der Liste und des Interfaces richtig sein!). Für die Verbindung zurück werden IP Routern verwendet (bspw. `ip route 20.77.64.0 255.255.224.0 20.77.255.254`).

---

## Multilayer Switch
### Inter VLAN Routing
Um Inter VLAN Routing verwenden zu können, müssen zuerst die VLANs inklusive IP Adressen konfiguriert sein, und anschließend `ip routing` eingegeben werden.

### IP Adresse einem Port zuweisen (wie bei einem Router)
Um aus einem Switchport einen Routerport zu machen wird `no switchport` eingegeben. 

---

## Server
### Server statische IP geben
Um einen Server eine statische IP Adresse zu geben, wird dieser geöffnet, dann unter _Config_ -> _Interface_ -> _FastEthernet0_ bzw. _GigabitEthernet0_ die IP Adresse unter _IP Configuration_ festlegen.
### Server als DHCP Server
Um einen Server als DHCP Server zu verwenden, muss dieser sich in einem Netzwerk befinden in dem folgendes erledigt werden:
1. Switch konfigurieren (IP Adresse an VLAN)
2. Server eine IP Adresse zuweisen, die im gleichen Netzwerk wie der Switch ist
3. DHCP am Server aktivieren und konfigurieren:
    - _Services_ -> _DHCP_
    - _DHCP_ -> On (Service aktivieren)
    - _Pool Name_ -> Kann man so lassen
    - _Default Gateway_ -> Wenn man einen Router hat, sollte man diesen hier eintragen
    - _DNS Server_ -> Hier kann man bspw. den DNS Server von Google (``8.8.8.8``) eintragen
    - _Start IP Address_ -> Hier die erste IP, die genutzt werden darf, eintragen
    - _Subnet Mask_ -> Hier die zum Netzwerk passende IP Adresse eintragen
    - _Maximum Number of Users_ -> Wenn man nicht bspw. maximal 10 PCs mit DHCP versorgen will, dann das hier eintragen
    - _TFTP Server_ -> Kann man so lassen
    - _WLC Address_ -> Kann man so lassen
    - Anschließend auf _Save_ klicken
    
    

~~~
                                                                                                                                                                             
                                                                                                                 l;;cdkxkO0KKKKXXNWWMWWNXXNMMWWMMMMMMMMMMMWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWMMMMMMMMMMMMMMMMMMMMMWKKXXNMMMMMMMWWWWNNNNNXXXXXXNNNNNNNNNNNNNNNWWWWMMMMMMMMMMMMMMMMMMMMMWNK0Oxl;''..      .'cdO00KKXXXXNNXXXXXXXXXXKKKKK00OOkdc,..                             ..... .  ..'',:dKNNWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWW
::dOOOkddkOOOOKNWWWWWWWWNXXK00XWMMMMMMMMWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWMMMMMMMMMMMMMMMMMMMMMWKKXNWMMMMMMMWWWWNNNNNNXXKKXXXXNNXXXXNNNNNNNNNWWWWWMMMMMMMMMMWMMMMMMWNXKkdc,'...      .,cdO00KKXXXXXXXXXXXXXXKKKKKKKKKK00Okd:'...                           .....     ..;;:cdKNNWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWW
:d0KK0OxdoddxxOKNNXXNWMWXXXKKKNWMMMMMMMWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWMMMMMMMMMMMMMMMMMMMMMWK0XNWMMMMMMMWWWWWNNNNNXXKKKKKXXXXXXXXXXNNNNNNNNNNWWWWMMMMWMWWMMMMMWWNX0xl;''....     .;lxOO00KKXXXXXXXXXXXXKKKKKKKKKKKKKK0Okdc,...                           ....     ..,:cclOXNWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWW
kO0KK00OxoooookKXXXXNWNKKKKXWWWWWMMMMMWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWMMMMMMMMMMMMMMMMMMMMWK0XXWMMMMMMMWWWWWNNNNNNXXKKKKKKKKXXXXXXXXXXXXXXNNNNNWWWWWWWWWWMWWWWNNKko:,'....      .,cdkOO00KKKXXXXXKKKKKKKKKKKKKKKKKKKK00OOxo:'..                           ..      ..';:loxKNWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWW
000XXXKOkxxxoclkKK0KNNKKKkdKMWNNWMMMMWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWMMMMMMMMMMMMMMMMMMMMWK0KXNMMMMMMMWWWWWWNNNNNXXKKKKKKKKKKXXXXXXXXNNNNNNNXNNNNNWWNNNNWWWWWWN0dc,'...        .':oxkOO00KKKKKKKKKKKKKKKKKKKKKKKKKKKK0000Okxo;...                                  ..';ccoONWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWW
xkO0KXXK00XNKxld0KOOK0OOOodXN0KXNMMMWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWMMMMMMMMMMMMMMMMMMMMWK0KXNMMMMMMMWWX0KNNNNNNXXXKKKKXXXXXKKXXXXXXXXNNNNNNNNNXXXNXXXXXXNNWWXkl;''...         .,lxkkOO00KKKKKKKKKKKKKKKKKKKKKKKKKKKK0000OOkxdl:'..                                ...,;:lONWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWW
ddddxkO00KNWWN00KXK000OOdlkXXXNWWMMWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWMMMMMMMMMMMMMMMMMMMMWK0KXNMMMMMMMWWXOkk0XNNNXXKKKKKXXXXXXXXXXXXXXXNNXXNNNNNNXXXK00KKKXNNN0o:,'....          .;lxkkkOO0000KKKKKKKKK000KKKKKKKKK000000000OOOkkxo:,..                              ...',;o0XNWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWW
ddddxxxxkkO0KXNXXXXKK0kdolokKNWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWMMMMMMMMMMMMMMMMMMMMNXKXNWMMMMMMWWX0O00k0NXXXKKKKKKXXXXXXXXXXXXXXXXXXNNNNNNNNXK00KKKKKXKxc;'.....          .':oxkkkkkOOO0000000000000000000000000000000OOOOkkkxoc;...                            ...''o0XNWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWW
odddxxxxxkkkkkO0KXNNX0xoodOXWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWMMMMMMMMMMMMMMMMMMMWNNNWMMMMMMMWWXOO000KXKO0KKKKKKKKXXXXXXXXXXXXXXXXXXXNNXXXXXKKKXXXKKOo;,'.....           .':odxxkkkkOOOOOOOOOOOOOO0000000000000OOOOOOOOOOOkkkxdol:,...                         ....'lOXNWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWW
,codddxxxkkkkkkkkkO0KKK0O0NMWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWMMMMMMMMMMMMMMMMMWWWNWMMMMMMMMWWX0O0OOKXKxddxkO000KKKXXXXXXXXXXXXNNNNXXXXXXXKK00KXXXKkl;,.......           .,:odxxkkkkkkkOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOkkkkkkxxxdolc;,.                        ....:okKWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWW
.';odddxxkkkkkkkkkkkkkOO0KXNWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWMMMMMMMMMMMMMMMMWKKXXNMMMMMMMWWX0O000KXKkdoooooodddxOKKXXXXXXXXNNNNNNNNNXXXXXXK000OOxc;,.......            .,codxxkkkkkkkkkkkOOOOOOOOOOOOOOOOOOOOOOkkkkkkkkkkkkkkxxxdooc;..                       ...cdxKWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWW
l:,;oddxxkkkkxkkkkkkkkkkkkkO0XNNNNNWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWMMMMMMMMMMMMMMMMWN00KKKNMMMMMWWX0O0O0KXKkddddddooooolldk0KXXXXXNNNNNNNNNNNXXXXXKK0kdlc;,.......             .,cdxkkkkkkkkkkkkkkkkkOOOOOOOOOOOkkkkkkkkkkkkkkkkkkkkkkxxxdol:,'.                       .;cloKWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWW
OkdodddxxxxxxxkkkkkkkkkkkO0KKXXXXNNNNNNNWWWWWWWWWWWWWWWWWWWWWWWWWWMMMMMMMMMMMMMMMWNXKXWWWWMMMMWWXOOOO0KXKkxxxxxxdddooooolloxO0XXXNNNNNNNNNNXXNXXXXKko:;;,.......              .,cdxkkkkkkkkkkkOOOOOOOOOOOOOOOOOkkkkkkkkkkkkkkkkkkkkkkkxxxdolc;..                      .,cldKWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWW
xxxkOkkxxxxdxxkkkkkkkkO0KXXXXXXXXXXXXXXKXNNNNWWWWWWWWWWWWWWWWWWWWWWWWMMMMMMMMMMMWWN0KWMMMMMMMWWXOOOO0KXKxdddxxxxxxdoooooooolloOXXNNNNNNNNNNXNNNXXKOo:;;,.......              ..,cdxkkkkOOkkkkkOOOOOOO00OOOOOOOOOkkkkkkkkxkkkkkkkkkkkkkkxxddlc:,.                     ..;clkXWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWW
odxxdxkOOkkxddxxkkkO0KXXXXXXXXXXXXXXXNXXKXNNNNNNWWWWWWWWWWWWWWWWWWWWWWWMMMMMMMMWWWNXXMMMMMMWWWKOkkO0KX0xoooddddxxdddoooooooolcd0XNNNNNNNNNNXNNXK0ko:;,'........               .'cdxxkkkkkkkkkkkOOOOOO00OO000OOOOOkkkkkkxkkkkkkkkkkxkkkkxxxdoc:;..                    ..'cd0NWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWW
ldkxodxxkkkOOkkkk0KXXXNNXXXXXXXXXXNNNNNNNXXNNNNNWWWWWWWWWWWWWWWWWWWWWWWWWWWMMWWWWWWWWMMMMMWWWKOkkkOKX0dllloooodolooddddddoooodOKXNNNNNNNNNNNXXKOdl:;,'........                 .,codxxxkkkkkkkkkOOOOOOOOOOOOOOOOOOkkkkkkkkkkkkkkkxkkkkkkxxdolc;'.                   ....;dKWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWW
:cclcoooxkxxkOKKKXXXXNNXXXXXNNNNNNNNNNNNNX00XNNNNNWWWWWWWWWWWWWWWWWWWWWWMMWWWWWWWNXNWMMMMWWWKOOOOOKX0dlloooooolccllooddddddxOKXXNNNNNNNNNNNXX0xlc;;,'........                    .';clodxxxxxxxkkkkkkkOOOOOOkkkkkkkkkkkkkkkkkkkkkkkxkkkxxxxdol;'.                   ....;xXWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWW
.,,,:ccldxxk0KXXXXXXXXXXXXXXNNNNNNNNNNNNNKOOOKNNNNWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWXKNMMMMWWWKO0000KX0dllooooloooollloooooddxOKXNNNNNNNNNNNXXKOxl:;;,'........                 ....  ..';clodddddddddooooooooooooodddxxdxxkkkkkkkkkkkxkkxxxxxxdl;'.                    ...;kNWWWWWWMMWWMWWWWWWWWWWWWWWWWWWWWWW
'...';;;:dOKKXXXXXXXXXXK0kkkKNNNNNNNNNNNX0OOOKNNNNNWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWNNMMMMWWWKO0000KXOdlloooooolcclllddoloodxOKXNNNNNNNNNNXXK0xoc;,,,'........                  ..........,;clooolc:;;,,,'.......''''',,,,;:cloodxkkxxxxxxkxxxxdc,..                     ..:0NWWWWWMMMMMMMMMWWWWWWWWWWWWWWWWWWW
lc;,...;d0KKXXXXXXXXK0koc::ckXNNNNNNNNNNKOOO0XNNNNNNWWWWWWWWWWWWWWWWWWWWWWWWWWWMXdOMMMWWW0xO000KXOdlloooolol::::cllodoooxOKXNNNNNNNNNNXX0kdc:;,,''........                     ....'.....;loool:;,''''..................',,;;coddxxxxxkkxxxxo;..                      ..:ONWWWWWMMMMMMMMMMMWMWWWWWWWWWWWWWWW
llllc:lkKKKXXXXXXXXKkoc:;::ckNNNNNNNNNNX0OOOKNNNNNNNNWWWWWWWWWWWWWWWWWWWWWWWWWMMWKXWWWWWWXOkxOKXOollooollllccccccccclooxOXXNNNNNNNNNXXKkdlc:;'''..........                      ..........:odddlc:;,,''''''',,,;;,,,,,,,;;;;:cllodxxxxxxxxxdl,..                     ...c0WWWWWWWWMMMMMMMMMMMMMWWWWWWWWWWWWW
:cclok0KKKKXXXXXXK0xl:::cllxKNNNNNNNNNX0OOO0XNXNNNNNNNWWWWWWWWWWWWWWWWWWWWWWWMMMMMMWWWWWWWNNXXXOollooolllllodollllccloxOXXNNNNNNNNXX0kdlc:;,'............                   ..............;dkkkdoc;,,''....',;;;;;;;:::c:::cccllodxxxxkkkxxoc,..                      .'lKWWWWWWWWWWWWMMMMMMMMMMMWMWWWWWWWWW
,,:dO0KKKKKXXXXXKOdc::cldkOKNNNNNNNNNNKOkkkkkxkO0KXNNNNNWWWWWWWWWWWWWWWWWWWWMMMMMMMWWWWWWWNNNXOollloollllldxollodollodOXNNNNNNNNXX0kxdl:;,''.............                  ........''',,,,lkOOkxolcc:;;,...';cllccc::::;;::cclloddxxxkkkkxxoc,..                     ..,dXWWWWWWWWWWWWWWMMMMMMMMMMMMMMMMWWWW
',oO000KKKKXXXXX0dc;:clldOXNNNNNNNXXNX0kkkkxxxxddxOXNNNNNNWWWWWWWWWWWWWWWWWMMMMMMMWWWWWWNNNNXkolllollllc:clolcokxolodOXNNNNNXXXK0kxdl:;,'...............                   .,;;;,,'''',,;cxOOOkxoloolcc:;'.....'',;;;;;;;;:cllooddxxkkkkxxdl:,.                      .':kXWWWWMMMWWWWWWWWWWMMMMMMMMMMMMMMMMW
'lxOO00KKKKKXXXKklccl::o0XNNNNNNXXXNNKOkkkoloddxxxkO0KNNNNNWWWWWWWWWWWWWWWMMMMMMMWWWWWWNNNNXkolllollll:;;;:cclodooox0XXNNNKkdooloxdc;,''..............                    ..,:cccccc:cccoxOOOOkxdoddolccc:;,'','.'',,;:cllllloddxxxxxxxxxdoc;..                      .,lONWMMMMMMMMMMWWWWWWWWWMMMMMMMMMMMMMM
oxkOOk0KKKKKXXXKxoloxdkKNNNXXXXXXNNNX0Okkkdollloodkkkxxk0XNNWWWWWWWWWWWWWMMMMMMMWWWWWWNNNNXkllllllllc:::::ccc:cclod0XXNXNKd::;;;;:;;,'.................                   .,:cllllllllloxO00OOkxddddddoolc:::::ccccllooddxkkxxxxxxxxdxxdolc;'..                     ..:xKWWMMMMMMMMMMMMMMWWWWWWWWMMMMMMMMMMM
xkOOxdOKKKKXXXXXKOO0KXNNNXXXXXXXNNNX0Okkkkxxkxdollloddddxk0KXNWWWWWWWWWWMMMMMMWWWWWWWNNNNKxllllllccolc:::ccccccclokO000OOxc;;,,,,''''................                    .';cllllolllloxO000Okkxxxdxxxxxxddolcccccclloddxkkkkkkkkkkxddolc;'...                     ..;x0XWWMMMMMMMMMMMMMMMMMWWWMWWWMMMMMMMMM
kkkxloO00KXXXXXXXXXNNNNXXXXXXXXNNNXKOkkkkxdxOOkkkxoooddxxxkxOXNWWWWWWWWMMMMMMWWWWWWNNNNN0dc:ccllccokxl:;,,,,,,,;,,;;;;;;::;;,,''''................                       .';cclllllllldk000Okkkxxxxxxxxxkkkkxxxdddddxxxxkkkkkkkkkkkxdoc;''...                      .'l0XNWWWMMMMMMMMMMMMMMMMMMMMMMMMMWMMMMMM
kxdllx0KKXXXXXXXXNNNNXXXXXXXXXNXXX0kxxkxdoddxkOkkkkOOOOkxxkkOXNNWWWWWWMMMMMMWWWWWWNNNNN0dcc:::ccccoo:,...............',;;;,,,''''................                         .,;:ccccc:cox0K00OkkkxxxxxxxxxxkkOOkkkkOOOOOOkkkkkkkkkkkxdoc,''...                       .:kKXWWWWMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMM
xdlox0KKXXXXXXXXXNNXXXXXXXXXXXXXKOdoodxxolddddxxxxkO000OOO00KXXNWWWWWWMMMMMWWWWWNNNNNN0dlllccc;,;:;..................,;;;,,,'''....................                       ..,;;;;,,;cxO000OOOkkxxddddddxxkkkOOOOOO0OOOOOOkkkkkkkxxdl;'''...                       .,o0XNWWMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMM
dodk0KXXXXXXXXXXXXXXXXXXXXXXXXX0xolllok0OkO0OOkxdxO000Ok0K0KKXXWWWWWWWMMMMWWWWNNNNNNX0dllllll:,','..................,;;,,,,'''...................                          ..''...'cxkO000OOOkkxxxddddddxxkkOOOO0000OOOOOOOkkkxxdoc,'''....                 .    ..:xKXNWWMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMM
dxOKKXXXXXXXXXXXXXKKKXKKXXXXXKkolccc::ldk0K0KKKOkkO00000KKKXXXNNWWWWWWWMWWWWWNNNNNNX0dllcllc:,',.. ...............';;;,,,''''...................                           .......;okkOOOOOOOkkkxxkxddddddxkkOOOOOOOOOOOOOOkkxdol:,'''....                      ..;okKNWWMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMM
O0KKKXXXXKKKXXXK0OO0KXKXXKKKOdlcccol,'';:ldkxxk00O0KKK00KXXXXXXXNNNNWWWWWWWNNNNNNNXOocccccc:;,,..  .............',;;,,,,'''....................                             ......:dkkkkkkkkkkkxxkkxdoodddodxkkkkkkkkkkkkkkxdol:,''''....                       ..:xOXWWMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMM
0KKXXKKKXKKXXKkoc:lxO0KKKK0xlc::ldkOxl,',;cdkkk0KKKK00KKXXXXXXXNXXXNNWWWWNNNNNNNXXOocccccc:,''..   ........ ...',;;,,,'''...................                                ...   'oxxkkkkkxddooooolllodddollodxxxxxxxxddddolc;,'''.....                       ..'lO0XWWMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMM
KKXXKKKKKXXKOdc::okOkkO00kocc:cldxkkOOxodkO00KKKKKKK0KXXXXXXXXXXXNNXXXXNNNNNNNNXXOl:::cc:,'....   .......  ...',;;,''''....................                                 ..    .':codddl:;,,,;;;:clloooolc:cloooooolllcc:;,''''''...                        ..,dKKXWMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMM
KKKKKKKKKK0xocc:okOOOOOxl:;;:cloddxdxk0KKKKKKKKKKKKKKXXXXXXXXXXXNNXXXXXKXXNNNXXXkl:::c:,'.....    ...........',;,,''''...................                                    .     ....','.......'',,;:ccccc::;;:lllllc;;,,,,'''''...                          ..:kXXNWMMMMMMMMMMMMMMMMMMMMMMMMMMMMWWWWMMMMM
KKKKKKKK0koccccokOOkkkxl:;;;:lllodxkkO0KKK000000KKKXXXXXXXXXXXXNNNXXXXXXXKKKKK0xl:::::'......     ..........',;,''''..................                                       ......  .................'''''''',,,;lllc:;;,,,'''''...                          ..'dOKXNWMMMMMMMMMMMMMMMMMMMMMMMMMMMMWWWWWWWWM
0000000Oxlccccokkkkkxdc:::ldollloxO0000KK000000KKXXXXXXXXXXXXXXNXXXXXXXXXNX0Okdc;;;;,'... ...    ..........,,,,,'.............  .....                                        .''...   ..',,''....'...............,clc::::,,'..''...                           ..:kKXNNWMMMMMMMMMMMMMMMMMMMMMMMMMMMMMWWWWWWWM
000000xoccc::oxkkxxxdc:;;lkOOOO0OO00000K0000000KXXXXXXXXXXXXXXXXXXXXXXXXXNN0xxxxl:;,... .  .     ........',,,',,.............   ...                                          .','...   .,;,,;;;,',''''.....  .  .,cc:::;,''..'...                            ..'cOXWWWWMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMWWWWWMM
0OOOkdlcc:::lxkkkxxdc::;cdOO000000000KK0000000XXXXXXXXXXXXXXXXXXXXXXXXXXXXN0xddoc,'...  .       .......'',,''''.............   ...                                            ';,...   ..,,',;;'.''.....     .. .;c:;;,'........                            ...,dXNWWWWMMMMMMMMMMMMMMMMMMMMMMMMMMWWWWWWWMMMM
OOkdlc::::;lxkkxxxoc:;;coodxxkkOO0000KK00000KXXXXXXXKKO0KXXXXXXXXXXXXXXXXXN0xl:,'....   .      ......'',,'''.............     ..                                              ';,.........''...''',''...  ..',..,:;,''.........                            ..';;kNWWWWMMMMMMMMMMMMMMMMMMMMMMMMMMWWMWWWWMMMMM
kxlc:::;,'cdxxxxxoc;;;:loooodkO00000000000KKXXXXX0Oxoc;;lkKXXXXXXXXXXXXXXXN0l,'...   .  .     ...''''',''''...........                                                        .;'...........''''''.....'',:ccc:;;;'..........                             ...;;cKWMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMWWWMMMMMMMMM
olc:::;,,:dxxxxdoc:;;:lddodxO000000000000KXXXXXKkl;'''',;oOXXXXXXXXXXKKKKOxc,...     ..  .   ...'''''''''............                                                         .,'...,;:;'','..'''',;:cllllooolcc:,.........                              ....;ckWMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMWNNNNNWMMWWMM
cc::::;;cdxxxxdlc::::clllldkOOO000000000KKXXXX0xc;,'';lxO0KKKKKKXXKOkOXXXKko;..       .  .. ...''..',,''...........   ...                                                     .,'..',;clcclllllloooooooddoooollc;'.'.....                                ....,xXMMMMMMMMMMMMMMMMMMMMMMMMMMMMMWNXNNXXNWWWWMMM
::::;;;coxxxxdlc::::cloodkkxkO0000000000KKKKKOo:;,''ck0000KKKKKK0xl:xXNNNNNNKx;.       .........''',,'...........    ...                                                      .,,..',;;::cccllllooddddxddoddolc;'''''....                               .....,dXWMMMMMMMMMMMMMMMMMMMMMMMMMMMWX0KXXXNWWWWMMMW
;:;;;;codxxxdlc:::::loooxkOOxxO0000K00O0KKKKkl;,,',:cldk00KKKK0d;''oKNNNNNNNNWXo.       ..','..';;,,'..........'. .  ..                                                        ';'..'',,;::cllloodddddddooool:;'''''...                                ....'.'dKNWMMMMMMMMMMMMMMMMMMMWMMMMMMWXKXNNWWWWMMMMWX
;;;;;coxxxddl:;;:ccloooodxkkkxox00KKKOO0KXX0oc;,,;:c::cx0KKKKx:...;0NNNNNNNNNNWN0c.    ...',;:;,,;;,,'..'......'.   ..                                                         .,,''''''',;:cclloodddooollc:;,,'''....                                  . ....cONWWWWMMMMMMMMMMMMMWNWWNWMMMMMMMMMMWWWMMMMNXK
;;;;coxxxxoc:;;;:clllloddddoo:;x0KKKKKKKKXXkolcccccc::o0KKKOl'.. .lXNNNNNNNNNNNWWXd,....';cc:ccc:;cllc;,,,'.....'.  ..   .                                                     .',,,;;;;,;:llloooddooodol:;,'''.....                                   ... ....cKWWWWMMMMMMMMMMMWWWWWWWWMMMMMMMMWWWWWWMMMWNN
,,;coxxxxdc;;;;;:cllloddolol;.cOKK00KKKKKX0dlllocldoclOKK0x:.. . .cXNNNNNNNNNNNNWWNKxolookKK00OOK00KXNX000Okxl;,,.  .....                                                      ...';cllc::lddxxkkxxdool;;,'......                                      .:;...'.'cONWWMMMMMMMMMMWNNNWWWMMMMMMMMMMWWWWWMMMMMMM
,;:odxxxdc;,,;;:cloooooolcc;.,xKK0OO00000K0kdlo:.'coxOO00x;...    'kNNNNNNNNNWWWWWWWNXXNNWWMMMMMMMMMMWWWNWWMMWNK0kc';:;:cclddo:.                                                ..';cllc:cldxkkkkxxdo:'......                                         .'ol. .''.'cxKNWMMMMMMMMWNNNNWWMMMWKO0KXNWWWWWMMMMMMMM
,:oddxxdc;,,;;:loodxxdoolc;''lO00OOOO0OOOk0K0OkocoxOOOOOd;....     ,kNNNNNNNNNWWWWWWWWWWWMMMMMMMMMMMWWNNNXXNWMMMMMWXKXNWMMWNKOOk;      .                                          ..,;;,;:clodddolc;'.....                                            .,dl. .',;;:lo0WWMMMMMWWNNWWWWMMN0xdxkkxxOKXXNWWMMMMMM
;lodddoc;,,;;:loddxxxdool;'.;xOOOOOOOOOkkO00000OOOOOOOko;.....     .cXWNNNNNNWWWWWWWWWWWMMMMMMMMMMMMMMMMMMWWWMMMMMMMMMMMMMWWNXKKd'.......                        ...                .....',;;;;,,'....                                                ..,,..;c,';,:lo0WWWWWWWWNNWWMWXOxxkOOkkk0KKKKOkOXWMMMM
cooodoc;,,;:cloddxkxxdol:,''lkkkkOOOkkkO00OkkkO00OOOOxc'.....       ;0NNNNWWNNNNNWWWWWMMMMMMMMMMMMMMMMMMMMWWMMMMMMMWWWWWWWWWWWNKOl.....     .                   ........                 ..........                                     .           ...... .;oc;'',cldXWWWWMMMMMMMNKkxk00OO0KXXX0kxddONMMMMM
llool:;,,,:lodddxkkxddo:,'':xkkkkkkkkkO00OkxxkOOOOOOkl'......       ,0WNNNWWWWWNNWWWMMMMMMMMMMMMMMMWWWWWWWWWWWWWWWWWWWNWWWNNNXK0KKo'.     .                    .'''''''....                                                             .           ....    ...'''':ookXNWWMMMMMWWWNNX0kxk0KKKOxoloxKNMMMMMM
lllc:,,,,:oddddxkkkxdo:,'';oxkkkkkxxkO000OOkxkkOOOOOk:........      cKWWWWWWWWMWWWMMMMMMMMMMMMMMMMWWWWWWWWWWWWWWWWWWNNNNNNNXKOddOX0c......                    ..';;:;;;,,,'.......                                                                  ...        .....,;o0NWMMMMWWWWWWWWNXXK0kdc:;cdOXWMMMMMMM
cc:;,,,,:odddddxkkxdo:,',:oxxkkkxxkOOOOOOOkkkkkkkxol:'........     .xWWWWWWWWMMMMMMMMMMMMMMMMMMMWWWWWWWWWWWWWWWWWNNNNNXXXK0OxollxKXk:''....                  ..';:cccc::;;;,,''''...                                                                           .......,oKWWWMWWWWWWWNWWWWWN0xc;:oONWMMMMMMMM
c:;,,,,:lddddoodxddl:,',cdxxxkkxxxkOOkkkxxxkkkxlc;,''.........     ;0WWWWWWMWWMMMMMMMMMMMMMMMMMWWWWWWWWWWWWWWWWWNNNXK00OOxoooldkO0X0c'....                 ..',;:ccclllccc::;,,,,''...                                                                          .......;d000KKXNWWNXXNWWWWNNNNKO0NWMMMMMMMMW
:,,,,,:loddollooool:,';ldxxxxxxxkkkkxddddodxdl;''',,''........ ...'dNWWWWWWWWMMMMMMMMMWWWMMWWWWWWWWWWWWWWWWWWNNXKOdocldoododkddO0KXOl;'......              .,,;::clllllllcc:;,;;;,,'............                                                                  .....';lxkkkO0KKKXNNWWNNNWWWWWWWWWMMMMMWWN
,,,,,:odddolcllolc;',coxxxxxddxkkkxddooooooc,.....''..............lXWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWNXKOoc::ccododx0XKKKKX0kl:;,........           .,;cccccooolcccc::::;;;,''''..............                                                             ......':okOkOK0O0XNNNNNNNNNWWWWWWWMMMMWNNN
,,,,:oddddollllcc;,:lodxxddodxkkkdoooooool;...........     .......cKWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWNNXXK0KKKKKXXXXNNNNNX0dlc;,,'..              ..',:cllllooooollc:;;,,,,,,'''............                                                               ......,ck00KK00KXXNNNNNNNNNWWWWMMMMWWNNNN
,,,coxxxdlllllc:;:ldddddddddxxxxdooollc:;,.               ........:0WWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWNNNNNNNNNNNNNNXx:;:,......          ..';;;;:loddoooodddolc:;;;;,,''.............        ....                                                    ..'''';oO0OkO0KXXNNNNNNNNNNWWMMMMWNNNNNN
',cdxkkdc:cccc:;codxxddddxxxkxdooollc;....            ...... .....,kWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWNNNWNNNNNNNXx:'.''..  ....       ';:llcc:cloddddoooolllllc:;,'''',,;;,,....     ...........                                                  ..,;,'';dkkkkOkkKXXNNNNNNNWWMMMMWNNNNNNN
,cxkkkdc;;:::::codddddddxxkkxdoolllc,......         ....  ........,oKWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWNNNNWNNNNNNNNNNNNNNXKk:''..'...     .....':cooodooolllloolllccc:;,'''',,;;,''.......   ............                                                   .''clc:,:k0000kkKXXXNNWNNWMMMMWNNNNNNNNN
cxOOOxc;;;;;;:loddoodddxxkkkxdooll:'....          ...    .......'',:kNWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWNNNNNNNNNNXXXXXXXXXXX0l;,.......     .,;codddxxxxxxdolcc:;;,;,,,;;::::;;'.....'...  ....'''','..                                                .  . .'.;ldo,,d0X0OOKK0Oxx0WWWMMMMWNNNNNNNNNN
kOOOx:,,,,,;:cloooooodxxkOOkxdool;.....       .....     ...'''''''''cONWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWNNNNNNNNXXXXXXXXXNNNXk;.'. .. ......,:clodxxxxxxxxxdoc:;;,;;;;;;:;;,,,''',,,'..  ...',;;,'..                                                     ..  ...;dkc,:d0K0O0KK0Ox0NWMMMWNNNNNNNNNNNN
00Od;'''',;:cccllloooddxxxxxollc,....       ............''''''......,xXWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWNNNNNNNNXXXXKXXNNNNNXOl'...  . .,;:cloooddxxxxxxddoolccccc::::;;:::::;;:c::,.  ...,,,'...                                                        .'. .,..lkdc,:x00OO000KXNWMMMWNNNNNNNNNNNNN
00x;....';::ccccclloooodddoolc;'....      ..........''''............,dXWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWNNNNNNNNXXXXXXNNNWWNXOo:...  .';:ccloddddddxxdddooolllccc::::::cccllc:ccc;.   .......                                                            ..  .;'.;dkx:'lO00KK000XWMMWWNNNNNNNNNWWWNN
0x;....',;;:::ccccccllloooolc,.....    .........''.............. ..':xXWWWWWWWWWWWWWNNNNNNNNWWWWWWWWWNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNXOc,'.  ..'',;clooodddoooolllllcc:::::cclllloooolcc:,..  ..                                                                        ...'cxOx:,oO000KKKXNWWNNNNNNNNNNWWWWWW
x,....,;;;;;::cccccccccllll:'.....   .........''''...''.............,oKWWWWWWWWWWWWWWWWNNNNNNNNWNNWWWNNNNNNNNNNNNNNNNNNNNNNNNNNNNNX0l......,cllc;:cclooodollccc:::;;::ccloooddddol:;,,'..                                .                                           .....';loc'.;oO00KKXNNNNNNNNXNNNWWWWWWW
'...',;;;;;;::cc:ccc:::ccc;......  ..  ............................';o0WWWWWWWWWWNNNNNNNNNNNNNNNNNWWWWWWNNNNNNNNNNNNNNNNNNNNNNNNNX0o,....':codooddolc;;:cccc::;;,,;;clooodddolc:;,,;;,..                                 ..                                         ..............,x000XNNNNNNNNXNWWNWWWWWWW
 ...',,;;;;;:cc:::::::::;'.....,. .   ............................',:dKWWWWWWWWWWWNNNNNNNNNWWWNNNNNWWWWWNNNNNNNWWWWWWWWWWWNNNNNNNXx;....',;clooollcc:;,,,,,;;;;:cllooddddoc:;;,;:;,..                                  ....                                          ..............lOKXNNNNNNNNNNNWNNWWWWNNW
   ..'',,;;;:::::;;;;;:;......lk:.. ..............................',cdKWWWWWWWWWWWWWNNNNNNNNWWWWWWWWNWWWNNNNNNWWWWWWWWWWWWWNNNNNNKo'....'',;;;;;;;;::::cccccclloddxxxdolc:;;:;,'..                                    ...                                              . ..........,xXNXXNNNNNNNNNNNWWWWWNWW
     ..',;;;;;;;::;;;;'.....,dKKc.................................';cxKWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWNNNXx:'',,;ccllolooooooooodddxxxdddolcc:;,,,,'..                                       .,'.                                                   ........:kKXXXNNNNNNNNNNNWWWWWWW
.    ....,,;;;;;;;;;,'.....:kXX0:.................................':okXWWWWWWWWWWWNNNNNNNNNNNWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWNNX0dc:::looddxddddddddddxxxxxdollc::;,'..                                            .,'.                                                     ........,ckKXXNNNNNNNNNNWWWWWW
'..........',,,,,,,'......o0XXXO,..............................   ..';cox0NWWWWWWWWWWWWWWWWWWNWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWNNXOdcccoddddxxdddxxxxxxxdolcc:;;'...                                                                                                            ...... .;xKNNNNNNNNNNWWWWWN
;,'..........''',,......,xKXXXXk,.................. .........         .;x0KKXNWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWNNNXOddodddddxxddodddooolc:,'...                                                                                                     ..          .....    .;kNNNNNNNWWWWNK0O
ccc,'........'','......cOXXXXXKx;. ........................          'dKXXKKKKXNWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWNNNXKOxxdddddollllcc;,,...                                                                                                         ...    ...   .....      'kNNNNNNNWNKd:;o
ddol:,........'......'oKXXXXXX0Ol........................           'xKKKKKKKXKKKXWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWNNNXX0xoooolccc;,..                                                                                                                .   ..',;'.......      .xXXXNNNNN0l,;ox
doool:,.............;kXXXXXXXK0d;..        ............            .;k0000KKKXXXKKKXNWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWNNNNXKkdoc:,..                                                                                                                       ....,,,;;,,'..      ;0XXXXXNN0o;:okO
dddddo:,...........l0XXNNXXXXKd,...   ..  ............            'oOK0kOO0KKKXXXXK0KXNWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWNNNXNNNKOl'.                                                                                                                         .....,ccccc::c;.    lXXXXXXX0xllodkk
llodxxoc;........,dKXXXNNNNNXx,.          ....                  .:0NNNX0OOO00KKKKXXKK0KXNWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWNNXXNNNXKkl.                                                                                                                          .;lddc:;:lxkkxoc;;xXXXXXXKkoldkOkl
coolddodo:......:OXXXXXXNNNXx;.           ....                  'kNNNNNXX00O0000KKKK00OkO0KXNWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWNNXKKXNNNX0x:..                                                                                                                      .,clc:;;cdkkkkkOOOO0KKXXXKkolodkkl:
ldoooooddo:....l0XXXXXXXXNKd;'.  ....     .                     .xNNNNNXK0KXXK0OOOOOOkkkxxxxOKNWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWNNK00KXNNXXOo,.                                                                                                                    ...,:cloodxkkkkOOOkkkkO0K0kooxxddl,c
oxxddddxxkxo;,oO0XXXNNNXNKd,.. ........                         .xNNNNXK00NWWNXK0OkkkkxxxxkkkkOKXWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWNX0OO0KXNXXKkl'.                                                                                                                  .,:odddoodxxxkOOkkkkOOOOOxc:dxkxc;:l
oxxodddddxxdodkOO0XNNNNNKo'..............                      .lXNNNXKKKKNWWWWWNX0OkxxddddxxkkkOKWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWNK0OO00XXXXX0x:,..                                                                                                              ..,lddddoddxxkkkkkkkOOkOOxc;dKkdl,cxd
ddolloddddddoloxOO0XNNNKo'..............                       cKNNNXXKKKKXWWWWWWWWNXKK00OkkxkO00XNWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWNNK00000KXXXXKkoc.                                                                                                              .,;:lodddxxkkkkkxxkkkkkkxc;oK0dc:dkdd
.......'',;ccc:lxOO0KNKl.............                         'ONNNNXXXKK00XNWWWWWWWWWWNNNXXKXXNNNWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWNXK000000KXXX0Od,.                 .,:c;.                                                                                     .;oc;codddxkkkxxxxkkkxkkxc,lK0d::kXXX0
           ..';cdkOO0Oc...........                           .oKXKKKXXXXXXKKKXXNWWWNNNNNNNNNNNWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWNNK00000000XXXK0d;.........        .oKNXk;.                                                                                  'ldxd:;oxxxkkkxxxxkkxxkOxc,c00d:;xXXXXX
              .'cxOOx;........                               .x0OOO00KKXXXXXXXKKXNNNNNNNNNNNWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWNNNXK0000OO0XXXK0d;..  .'.           ,xXNXx,                                                                               .,oxxxdc:dxkkkxxxxkkkxxkOkc,:O0d:;dXXXXXX
....          .':dOd,......                                  .cxxxkOOO0KKKXXXXXXKKKKKKKKKKNWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWNNNXK0000OO0KXXKkl'.    .coooolllcc::lkXXX0o.                                                                            .cxxxxxdclkkkkxxxxOOkxxk0kl,;k0d:;oKXXXXXX
..........    'odoc'..                                        .:ooddxkkOO00KKKXXKKK00OOOkk0NNNNNNNNNNNWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWWNNNXXK0000OO00KOo,.     'dKNNNNNXXXXXXXXXXXO;                                                                          .cxkkxxxocdOkxxxxdddxxkO0Ol,;k0d:,oKXXXXXXX
..............','.                                       .';cldk00kddddxxkkOOO00KKKK00OOkxkKNNNNNNNNNNNWWNNNNNNNNNNNNWWNWWWWWWWWWWWWWWWWWWNNNNNNXKK00OOOO0kl'.      ,oooddddddddddddo:. .                                                                      .,oxxxxxdlldkkxxxxxxxxkkO0Oo,,x0x:,l0XXXXXXXX
..............                                        .';oOXNNWWWNK00OxdoddxxxkkOOOOOkkxddox0XXXXXXXNNNNNNNNNNNNNNNNNNNNNNWNWWWWWWWWWWWWWNNNNNNNNXK00OOOkkxl,.          ................'.                                                                    .;dxxdddoloxkkxkkxxxxkkO00Oo,,d0x:,c0XXXXXXXXX
  ..  .                                              ..:o0XNWWWWWNKKK0kdolllooddddxddddoloooox0XXXKXXXXNNNXXXXXXXNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNXKKK000OOOkd:..           ..'''',,,,,,'..                                                                     .;dxxdolodxkkxxxxkkxkkOO000d;,o0x:,cOXKXXKXXXXX
                                   ...              .':oOXWWWWWWWNX0OkxdoollllllooooooolllooodxO0KKKKOkO0KX0OOO0000K0000KXXXXXXNNNNNXXXXXXXXK0OO00000OOOOkx:.              ..''',,,'..                                                                      .;dxdoooddxxxxxxxxkxxkO0000d;,lOxc,:OKKXXKKXXXXX
                                  ...             ..,cokKNWWWWWWWWN0xdooooooooolllllloddxxkkOOO0KKKKkdoddxkxddxxkkkkkxxxkkkOO00KKKKKKKKKK00OOOO00000OOOOkkkd'                ..,,..                                                                        .:dxddddxxxxxxxxxxxxxxk0000x:'cOkc,:kKKKKKKXXXXXX
                                 .               ...',;dKWWWWWWWWWNX0kdooooooooollllllodkOO000KKKKK0OxdooooddxxkkkOOOOOOOOOOO000K0000000000000KKKKK00OOOkkkxl.                 ..                                                                         .:xxxxxxxxkxxxxxxxxxxxk0000x:'cOkc,;xKKKKKKXXXXXXX
                             ..                  . ..,l0NWWWWWWWWWNNNXKOOkxdddooooloooodxO00KXXXXXXKK0OkdoodxxxxkkkOOOOOOOO000KKKKKKKKKKKKKKKKKKKKKKK00Okkxxxc                                                                                            ;dxxxxkkkkkxxxxxxxxdxkO000kc':kkc,;xKKKKKKXXXXXXXX
                        ....                       .,:oKWWWWWWWWWWNNNNXK00OkkxxxxddoooddkO0KXXXXXXXXXXXK0OkxxxddxkkkkOOOOkkOO000KKKK0000000KK000KKKKKK0Okxxxkx;                                                                                          ,dxxxxxkkkxxxxkkxxxxxOOOOOxc',xkl''cxxO000000KKXXXX
                                                  ...;OWWWWWWWWWWWWWNNNX0kxxdddxxddxxkkO00KKXXXXXXXXXXXXXKKK0OkkkkkkkkOOOOOOOOOO000OOkkkOOOOOOOO00KKKK0Oxdolooc'                                                                                        ,oxxxxkkkkxxxdxxxxdxkOOOOOkl''oxc,.....:c:lxkkkO00KK
                                                    ,OWWWWWWWWWWWWWWNNNXKOkkxxddxxxkkOO000KKXXXXXXKKKXXK0KXXXXXK00OkkkkkOOOkkkOkkkkxxxxxxxxxxxkkkkOOOkkxdoc;col,.                                                                                      'oxxxxkkkxxxxxdoodxxkOOOkkkd;.;c:,.....::'..;ldddxkkO
                                                   ,OWWWWWWWWWWWWWWWNNNXKKKXKK0OOOOOOOO000KKKKKKK0000Okkxk0XNNNNXXKKKKKK00OOkkkkxddddddddddddddxxxxxddoodoccoxo;.                                                                                     .lkkkkkkkkxxxxxdddxkOOOkkkkko,..;,....,lolc:,',clllodx
                                                  ,kNWWWWWWWWWWWWWWWNNNXKKXNNNXXKKK00OO00KKKKKK00000kxkOkkO0KXXXKKXNNNNNNNXXKK0OOkkxdooddddxxxkkkxxdoooddolldxl,.                                                                                    .lxkkkkkkxxxxxxxxxxkOOOOOkkkkd;..,..:lodxollll:',:c:;;l
                                                .;ONWWWWWWWWWWWWWWWWNNNXXXNWWNNNXXKK00O00KK0000000000OkOOO0000000XNNWWWWWWWWWNNNXXXX0kxxxkkkOOOOOOkxxxxxddoldxc.                                                                                    .lxxxkkkxxxxxxxdxxkkOOOOOOOkkkkl,,'.cddkxoooool:,,;lol:c
                                               .cONWWWWWWWWWWWWWWWWNNNNXNNWWWWNNNXXKK0000KK000OO00KKK0OkOO0KK0K0OKXNWWWWWWWWWWNNNNNNXKOOOO00000000OOOkkkxxlldl'                                                                                    .lxxxkkkxxxxdddddxkOOOOOOOOOOkkkxo:'.:xkxoooollc:;,,cxkdl
                                             ..:d0XNWWWWWWWWWWWWWWWNNNNNNWWWWWNNNXXKK00OOO00000OOOO0OOOkO0KKKKKK00KXNWWWWWWWWWNNNNNNXK0000KKKKKKK000Okkxxdccc.                                                                                    'oxxkkkxxxxddddddxkOOOOOOOOOOOOkkkkd;.;xxoooolcc::;;,,lxxd
                    .                       ..':okXWWWWNNNWWWWWWWWWNNNXXNWWWWWWNNNXKK0OOkkkO000OkkkkkxdxO0KKKKKKKKKKXXNNNNNNNNWNNNNXK00KKKKKKKKK0000Okxdo:''.                                                                                   .;dxxkkkxxxdddoodxkkOOOOOOOOOOOOOkkkkkocc::clllc::::::;,,lxx
                                             .',ckXWWWNNNWWWWWWWWWWNNNXNNWWWWWWNNNXK00OOkxkO0000kxxkkkxxkOO0000KKKXXKKXXXXXXXNNNNXXKKKKKKKKKKK000OOOkdol;...                                                                                   .cxkkkkxxdddddoodxkOOOOOOOOOOOOOkkkkkkkkkl,.,;;:cccc:::;,';lo
                                              ..c0NNNNNWWWWNNNWWWWNNNNXNWWWWWWWNNNXK00OkxxkO0000OxxxkkkkkkxxxkOO00KKXXXXKKKKKKKKKKKKKKKKKKKK000OOOkxollc,.                                                                                   .;okkkkkxddddddodxkkkOOOOOOOOOOOkkkkkkkkkOkxdc;'..;cccc::;;'';c
                                              .'oKNNNNWWWWNNNNWWWNNNNNNNWWWWWWWNNNXK00OkxkOO000000kxxxxxxxdoodxxkOO00KKKKKKKKKKKKKKKKKKK000OOkxdooolc;,'.                                                                                  .:dOOkOOkxdddddoddxkkkkkOOOOOOOOkkkkxxxxkkkkxxkkxdc,.';:c::;;,''c
                                             ..,o0XNNNWWWNNNNWWNNNNNNXNWWWWWWWWNNNXXK0OkkOO00000000OkxxxxxxddooddxxkkOO00KKKKXXXXKKK0000Okxdoolllc:,.                                                                                    .:x00000OOkxxxddddxxkkkkkkkOOOkkkkkkkxxxxxkkkxxkkkkkkxoc;:cc:;;,'';
                                              .,oOKNNNNNNNNNNNNNNNNNXXNWWWWWWWWNNNXXK0OkOO00000000000OkxxxxxxddooooddxxxkOO0000KK00OOOkkdooooddl;..                                                                                    .:xKKK00000OOOkkkkkkkkkkkkkkkkOOOkkkkxxxxxxkkkxxkkOOkkxxxxdollc:;,'..
                                              .,lx0XNNNNNNNNNNNWNNNNXXNWWWWWWWWNNNXXK0OOO00000000000000OkxdxxxxdoolllooodxkkkOOOOOkxxdoooodxdo:.                                                                                     .;xKKKK00000OOOOOkkkOkkkkkkkkkkkkkkkkkxxxxxxkkxxxkkkOkkkxdddollcldc''..
                                             ..':o0XNNNNNNNNNNNNNNNNXNWWWWWWWWWNNNXXK0OOO0000000KKK000000Okxddddoolccccllodddxxkkxdolcc::;;;'.                                                                                       ;kKKKK00000OOOOOdlcldxkkkkkkkkkkkkkkkkxxxxxkkxxxkkkkkkkxxdocccloo:',:,.
                                            ...,ckXNNNNNNNNNNNNNNNNXXNWWWWWWWWWNNNXXK0OOOO00000000000000000Okdoollccccccllooloolc;,'...                                                                                             ,oddxkO0000000OOo:::::clodxkkOkkkkkkkxxxxxxkkxxkkkkkkkxxdddollodl,'coc;'
                                            ...lkKXNNXXXXXXXXXNNNNNXXNWWWWWWWWWWNNNXK0OOOOO000000000000000000Okxddddxxxxxxxxdo:.                                                                                                  .;loooooodxxxxxxxdc::::::::cclodxkkkkxxxxxxxkkxxkkkkkkkkxxddolodd:';oolc;'
                                             .:dOKXXXXXXXXXXXXXNNNXXXWWWWWWWWWWWNNNXXKOOOOOOO0000000KKKK000000000000000OOOOOkko;.                                                                                               .,loolloolllllllllcc::::::;;:::::::clodxxxxxxkkxkkkkkkkkkxxxollldl,'cdolc:;,
                                            .;cxOO00KXXXXXXXXXXXNNXXNWWWWWWWWWWWNNNNXKOkkkkOOOO000000KKKK0000000000000000000OOkdc.                                                                                            .;dkxxdollllcllllcccccccccc::::::::::;,,;:lodxkkkkkkkkkkkkxxdolloo:';odolc:;,'
                                           ....,;ok0KXKXXXXXXXXXXXXXNWWWWWWWWWWNNNNNXX0kkkkkkOOOO000000000KKKKKKKKK0000000000OOkdl;..                                                                                       .:okOOOkkxddooooollllllccllcccccc::;;;;,''''',cdddkkkkkkkkxxxdllodl,'cddolcc:,'.
                                               .,oO0KKKXXXKKKXXXXXXXNWWWWWWWWWWNNNNXXK0kkkkkkkOOOOOOO0000000000KKKKK000000000OOOkxdoc;..                                                                                  .ck0OkO00OOOOkxxxdddoddooollllllllcccc:;,,''''';c:,;codxkkkkxxooloo:';odollc:;,'..
                                               .;ok00KKKKKKKKKKKXXNNNWWWWWWWWWNNNNNNXXOkxxxkkkkOOOOOOOOO000000000KKK0000000OOOOOOkxddoc,.                                                                               .;x0000OkO00OOOOOkkkxxddddddooooooollllccc:;,,,;lol:;;;;::lodxdolodl,'cddoolc:;,....
                                              ..;ldkO000000000KKXXXXNWWWWWWWWWNNNNNNNKOxxxxxxkkkkkOOOkkOOOOO0O000000000000OOOOOOkkkxdol:'.                                                                             .lO000000OkO000OOOOOOOkkxxxxxddodddooolllccc::;:lddlccc:::;;;cllloo:',oddollc:;,.....
                                              ...';oxkkOO00000KKXXXXNWWWWWWWWNNWWNNNX0xddodxxkkkkkkkkkkkkkOOOOO00000000OOOOkkkkxxxxdolc;.                                                                             .c0KK000000OkO0000OOOOOOOOkkkkxxddodddoooolllcc:ldollllcccc::cloool,'codoollc:;'......
                                                  .;:lddxOO0000KKKXNWWWWWWWWWWWWWNNNKOOOkkxxxxxkkkkkkkkOOOOOOOOOOOOOOOkkkkkxxxxxxxdolc,.                                                                              ;OK00KKKK000OkO000000OOOOOOOOOOkkxdddddooooollloolcllloolcccloodo:',ldoollcc:,'...... 
                                                   ..,::lxkkOO0000XNWWWWWWWWWWWWWNNNK0KXXXXXKK0OkkkkOOOOOOOOOOOOkkOOkkxxxxxxxxxxxdol:'.                                                                              .xKK000000K000OkO00OO00OOOOOOOOOOOkkxxxdddoooooddolccccllooollodl,.;oooollc:;,'......  
                                                     ..';lddxkOO00KNWWWWWWWWWWWWWWNNKKXNWWWWWWWNXKK0000000000000OOOkkkxxxxxxxxxxddo:.                                                                               .l0KKK0000000KK0OkO0000OOOOOOOOOOOOOOkkxxxxdddddxdoollllccclooooc''clclllc:;,''......   
..                                       ..          ....,;;:cldk0XWWWWWWWWWWWNNWWNNXXNWWWWWWWWWWWWNNNXK00000KK0000OOOkxxxxxxxxxdo;.                                                                                :O00KKK00000000K0OkO000000OOOO00OOOOOOOkkkkkxxxxddooooollllcloo;.;lccclc;,;,'.......    
;'.   ..'.........         ..    ...........'',,,'''.....,;;;;;cdOKXNNNWWWWWWNNNNNNXXNWWWWWWWWWWWWNNNXKK000KKKKKKKK000OOkxxxxxxo;.                                                                                 ,k000000000000OO000kk00000000OO00OOOOOOOOOkkkkkkxxxdooddoollloc''clcclc:,',,.......      
'.  ..',,'...''''..     ...,;clldxkkkkoldolxkkxdoloddddoloolloolcccldxO0XNNWNNNNNNXXNWWWWWWWWWWNXXXXXXXK000KKKKKKKKKK000OOkxdl,.                                                                                  ;k000000000000000O000kk00000000OOO0OOOOOOOOOkkOkkkkxxddoollloo;';lc:cc:,'','.......       
    ..........''..    ......cxkOOO00Odoxolddooc:cclddxxololccolccldxxkOOOO000KXXXKKXNWWNWWWWWWWNXKK0KXK0O000KKKKKKKKKKKK00Od:.                                                                                   :OK0000000000000000OO00kk00000000OOO0000OOOOOOOkkkkkkkxxdooodc''cl::cc;'','.......         
 ..,,,,''..',,'.   ..... ..colcodxkkxdoolllooddoc,'',;oxkkkxkOkkkkkkkOOkxdlclldkOOOOO00O0XNNNNNNXKOkxOOOOO000KKKKXXXXKKKK0kl,..                                                                                .lO00000000000OOOOOOOOOO00kkO0000000OOOOO0OOOOOkkkkkkkkkkxxxdo;.;lc::c:;'','.......          
.,:::;;;;;::::,...,;,..,;ccclodxxxddoooooodxxxdc,,,,cx00000OOxoolllldxxkxooolloooxdccccodxOKXXXX0kxdodddkkO000KKKKXXXXXXKK0x:.                                                                               .;x0000000OOOOOOOOOOOOOOOOO0Okk000OOOOOOOOOOOOkkkkkkOOOOOkkkxxl'':l::cc;,','.......            
,;;;,,,,,:::;'.':clllodolccoddoolloodoooodddoc;'',cok0000OkdooddolllodxkOOxodkkxxxxdlcoddoooddolcllllc:cdxkOO00KKKKXXXXXXXKOd;.                                                                            .:x0000000OOOOOOOOOOOOOOOOOOOOOOkk00000OOOOkkOOOOOOOOOOOOOOOkkd;.,lc::c:,''''.......             
,,,''''',;;:::coxxxdoc;;:odoollllllclooddol:,'';lxO0000OxdddollloxkkxdoodxOOOkkkxxxxxxxdddddc'...':llc,,:oxkO000KKKXXXXXXXXK0d;.                                                                         .ckKKKK000000OOOOOOO0000000OOOOOOOOkO000000OOOkkOOOOOOOOOOOOOkxl'':l::cc;''''.......              .
;;,,,,,;cldkkxdooolllodxxxxxxkkkkkxoc;''.....;okO0OOOxoodddolodxxxxxxxdlccoxkdolodo:;:::ccc:..   .'::,',,cdxkO000KKKXXXXXXXXK0kc.                                                        .'clooooooolcclxOKKKKKKKK000000000000000000OOOOOOOOkxddooodxkOOOkkOOOOOOOOOOkd:.,cc::c:,'''........               .
:;,;;;lxO0kolclodxkkkxxxxxxkkkkkxdolc;,....'cxkOOOkxolcc::coxxxddooodddoc;'',,,,,,'............',;:::,...;ldxkO00KKKXXXXXXXXXX0xc.                                           ..... ...';lx0KKKXXXXXXXXXKKKKKKKKKKKK0000000000000000000OOOOkxdl:'.....',:odxOOOOOOOOOxl'':c::cc;'.''.......                  
...';dO0kl;:oxkkkkkkxxxxxxxdolc;,,;;;;;clodk0000Okkkxo:,;ccclooolcccllc;.   ...''...'..''';:cllc::c:;,,..,:loxOO00KKXXXXXXXNXXX0x:.                                 'looooooxkOOkxxxkO0KKK00000KKXXXXXXXKKKKKKKKKKKKK0000KK000000000000OOkxxo:,....    .;,';coxkOOkd:',cc::c:,'''........                 ..
..'cx0Od:,cxkxddddxkkxxxxdol:,'''...;lxO0KKKXKOkkkxollol::;;:ccc:;,'...     ....',,;:;;:::ccclol;'',,'','';clldO00KKXXXXXXNNNXXX0x:.                                ,OKKKKKKKKKKKKKKKKKKKKK000000KKKKKKXXXKKKKKKKKKKKKKK000000KK000KKK00Oxdddcokxd:..';:,.  ..';ldl,.;c:;::;'.'........                  ...
:,;dkko:::clllcccccllollcc::::;,;clldO00KKK0kddxxoclddol:,,;:;,...          .'',,;;,;:cc:::;;:cc:'..''',;,',:::lk00KKXXXXNNXXXXXXKx;.                               ,OKKKKKKKKKKKKKKKKKKKKK00KKKKKKKXXXKXXXXKKKKKKKKKKKKK0000KKKK00KKKKK0kdddloOOkd,.:kkdl;..   ...,cc;:::,..........                   ....
.,lddollccccllccc:,.....',;:ldxO000000KK0Oxodxd:;cddoooc;,,'..       .    ..'''.''..'';:::;,,,;:::'.';;,;,'..,,':k0KKXXXXNXXXXXNXXKx:.                              c0KKKKKKKKKKKKKKKKKKKKKKKXXXXXXXXXXXKKKXXKKKKKKKKKKKKKK0000KKKKK00KKKOdodoldOOx;':xOOOOxo:'. .,c:;;:;'.........                    ...  
':ooc:ccclddddoc;..     ..:okO0KKKKKKKK0xodkxc;coooooooc,..      .....................',;:;,,,;::c:'',;,'..  ....lOKKXXXXXXXXXNNNXX0d;.                            'kKKKKKKKKKKKKKKKKKXXXXXXXXXXXXXXXXXXKKKKKKKKKK0KKKKKKKKKK00000K0000000koodoldOOo:,lOOOOOkd:..,;,,;;'..........                     ..   
.:lc:;:cldddddl;'.... .'cdxxxxkOO0000Oxodkkolodoolodddd:.     ....''''.....''...........',;;,;;::cc;.......   ...:x0KXXXXXXXNNNNNNXXOl.                           .dK0000000KKKKXXXXXXXXXXXXXXXXXXXXXXKKKKKKKKKKKKK00000KKK000K000000000000xllooldOx;.,k0OOxo,.,;,,,;,'..........                           
.:c:;;:codddl:,'..'.';oxxxxxxxxxxxkxdodkkolokkkdlloxxxo,.     .',,''''.....,,,,,.........',,..,:,';,.  .......;oc;dOKXXXXXXNNNNNXXXXKk:.                         'xKK000KKKKKKKKXXXXXXXXXXXXXKKKKKKKKKKKKKKKKXKKKKKK0000OOO0KKKKK00000000000xc:lolodc..oOkdc''::,,,,'..........                             
;c:;,;:clolc,...''';okOOkxxxxxdddl;':dxlcoxOOkxxdoddxo:''... ..',;;,'.''...,::;;'....................  ..,:c::cdxooO0KXXXXXNNNNNXXNXKOl'.                      .cOKKKKKKKKKKKKKKKKXXXXKKKKKKKKKKKXKKKKKKKKKKKKKKKKK00OOOOO00KKKKKKK0000000000xc;clll:..:xo;.,c:,;;,..........      .;;..              ..    
llc:::cllc;.....';lxOOOOOOkkxddl;.,cl;';dOOOkkkxxxxxl,.''''',;;''',;''''''..,::;..               ......'clll:;:oxxxk0KXXXXXXNNNNXNNXKOd;.                    .;xKXKKKKKKKKKKKKKKKKKKKKKKKKKKKKXXXXKKKKKKKKKKKKKK0000KKKKKKKKK0000000000000OO00kc,:llc;..;,.;c;,;;'.........        .cdl'.           . ...   
olccllool;......:dkOOOOOOOkkkdc'':l;.':lodkOkkkkkkko,.....';cll;'...,,'',;,'.',..     ....       ..':c;:lollc:;:loxO0KXXXNNXNNNNXXNXKOo:.                  .,dKXXXXXKKKKKKKKKKKKKK0KKKXKKKKKKKKKXKKKKKKKKKKKKKKKKKKKKKKKKKK000OOOkkxkO000OOOOOOkl;,:cc:'.';;,;;,..........      .',..:dd:.        ...       
ooodddo:'.....;oxkkkkkOOkkkxo::lo:'':oolllodkkkkkxl,'.....'collc;...'',,,,;;'. ... .';:::,......';:cllcclllc;'.,:lxO0KXXXNNXNNNNXXNXX0d,.                'ck0KKXXXXXXKKKKKKKKKKKKKKKKXXXKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKK0Oxl;'....';oOOOOOkxOOkc'';::;,'',,'.........        .;c:,';dko'.      ..        
ooddoc,'....'cdkkkkxxkkkkkd:;ldl;;looolllllloxxxo:'.......;clllc:'...,,,'','.   ..':loooc,'''''',;;:cllccc;;,'.;cok00KXXXNNXXNNNNNNXX0kc.           .';cx0XXKKKKKXXXXXKKKKKKKKKKKKKKKKKXXXKKK000000KKKKKKKKKKKKKKKKKKKKKKK00Oo,.       .,cdkkkxkOOk:..',:;,''..........        ...',:;,,lkx;.               
cclc;.....':dkkxxkkkkxxxd:;cdo:;lxxxdoooolllllc;'.''....,:::cllc:;...,,,'...    .;oooddol;,'''....',:cc,,;;;;,,cdxO0KKXXXNNNXXNNNNXXK0kl,.      ..;okKXXXXXXXKKKKKKXXXKKKKKKKKKKKKKKKKKKKXKKK00KKKKKKKKKKKKKKKKKKKKKKKKKK00Ox:.           .;ddxkkkxl,....,;;,........       ...',,,'':c;,:xkc.              
:::,.....,lxkkkkkkxxxkxc,;loc;:dxxxxxdoolllc:,. ......';ccc::c::::,..,;,.       'ldoldddo;...........,,'';;:lc:cdxO0KKXXXNNNXXXNNNXXK0kxdlc:;:cox0XXXXXXXXXKKXXKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKXKKKKKKKKKKKKKKKKKKK0Okkxkkkkkxl'.            .c;;oxxxdl'  ...',;'.....      ..''.......':ol;;dOo'.            
::,.....,okkkkkkkkkxxdc':do,'cdxxxxddxddlll:.    .....,:c:cc::;;:::,'',.      .':ldooodd:............,'.':llllclxkO0KKXXXNNNXXXNXXXXK0kk0KKKKKKXKKKXXXXXXXXXKKXXKKKKKKKKXXXKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKK0OO000kdlccccccccc,.             .'..:dxdoc.  .....','...       ....       .'cxo,,d0d'.           
                                                                                                                                                                                                                                                            
~~~
