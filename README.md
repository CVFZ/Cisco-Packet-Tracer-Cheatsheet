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
                                                                                                                                                                       ++---::.    .                                  ..        ........                .-+##@@@%+::...........::-=#%@@@@@@@@@@@@@@%%%%%#*=.                 
+:.:==--..  ....                              ..         ............           .-*#%%@@#=::..............::=#%@@@@@@@@@@@@@%%@%%%*+-                 
::..:--+::...= .                             ..         ................       :+##%@@@%+-::..............:::-+#%@@@@@@@@@@@@@@@%%#+=.                
==--:. ...::--.                             ..     .:: ............... ...:...=*#%%@@@@#=--:::::::::::.:::::::--+#%@@@@@@@@@@@@@@%%*+.                
+=-----::..:=:                                    .::::::................:..-+##%%@@@@@#=---:::::::::::::::::::---=*#%@@@@@@@@@@@%%#+.                
**=--------::..                           ...    .::..=====-:....... ....::=*##%%@@@@@@#=------::::::::::::---------=*%@@@@@@@@@@@%#=:                
--=---------::.... .                      .     .::..----=====-...  ......=*##%%@@@@@@@#=-----::::::::::------------=+*%@@@@@@@@@@%*+:                
=--------::...........                    .    .-:.:====-=======.   ....:=*##%%@@@@@@@@#=------::::::::::------------=+#@@@@@@@@%@%#=.                
*++=--::............ :.                  .    .::.:=====++===-:..   ...-+*##%%%@@@@@@@@@%*+=--------------------------+*%@@@@@@@@@%%=                 
*##*+:......--:.    .::.                 .   .::.:====+++====:..   ..:=**###%%@@@@@@@@%%%%%#+==+***#%%#####*++=-------+#@@@@@@@@@@%%-                 
+++=:.....-+*+-     ::.                  .   ::.:====++++++=:..  ..:=+*##%#%%@@@@@@@@@%%%%%%#=-=+*####********+==-----*%@@@@@@@@@@%#:                 
*+::.....=++=:. ....:---:.                     :===++====+=:. ...:-+*#####%%@@@@@@@@@%#####**-:-++**%#*+++***++=-----=*%@@@@@@@@@@%*.                 
*-::....:+++:......:-===--:..                .-===++*++===:...====*##%%###%%@@@@@@@@@#++++++-::-===++*#****+===-----=+#%@@@@@@@@@@%+.                 
-:--.....:. ..... ::----====-:.             .-++++=+*++++=---+**###%%%###%%@@@@@@@@@%*+====-::-------==++==-------==*#%@@@@@@@@@@%*:                  
-==:.............:--==----::-::.           .-+++++*#%%#####****###%%#%##%%@@@@@@@@@@@#*+++=:::-------:--::::-----=+##%@@@@@@@@%@@%-.                  
=-:.............-++-::::-:::::..          .-+++*#%%%%%%%%#***###%%%%##%%%@@@@@@@@@@@@%###+-:::---==---:::::::---=*##%@@@@@@@@@%@%*:                   
:.......--:...:++=+**=--::::.....       ..=+++*#%@%%%%%%#***###%%%%##%%%@@@@@@@@@@@@@@#%%=--------====--------=+###%@@@@@@@@@%@@#=.                   
......-++--::=++=----::...:..........  ..=++*##%@%%%%%##**###%%%%%%%%%@@@@@@@@@@@@@@@@%@@#*==*****+==+++===++**###%@@@@@@@@@%@@%#:.                   
.::.:=++-:--+*++=-::.:::...............:+**#%%%@@%%%%##*###%%%%%%%%%%@@@@@@@@@@@@@@@@@%#%%%%#%%%%#######*++***###%@@@@@@@@@@@@@%=.                    
:::-+++---=+*-::::::::::..............--=*#%%@%@@%%%##**##%%%%%%%%%@@@@@@@@@@@@@@@@@@@@*#%@%****###%%@%%*++*###%%@@@@@@@@@@@@@%#:                     
:-++**---=**==-:::::::....:-:.........-*#%%@%@@@%#######%%%%%%@%%%@@@@@@@@@@@@@@@@@@@@@*#%%%#######%%#*#**###%%%@@@@@@@@@@@@@%**                      
=++**=--=+*+==-::::::...-*#*+:.......-*%%@@%@@%%######%#%%%%%@@@@@@@@@@@@@@@@@@@@@@@@@@###********+====+*##%%%@@@@@@@@@@@@@@%%*:                .     
++**=--=+++==--:::::..:=*#+::...:+- . .=%@@@%#####*##%%%%%@%@@@@@@@@@@@@@@@@@@@@@@@@@@@###**++====--===*###%@@@@@@@@@@@@@@@%%##:              ...     
***=-=+**+===--=:..:..=***++:.:+%*      :#@%%#*+***###%%#@%@@@@@@@@@@@@@@@@@@@@@@@@@@@@%*###*++=====++*###%@@@@@@@@@@@@@@@@%%#%-                     .
**=--+**+=====*+.::...=++==-.-#%%+        :=-:.:....::-=+%%%%%#%@@@@@@@@@@@@@@@@@@@@@@@@#*+++=---==**#%%@@@@@@@@@@@@@@@@@@@%+%##-                     
*=--+**+==-=+*#::::::::-=-::=#%@@%=                       ..  .:+%@%%@@@@@@@@@@@@@@@@@@@%%#**+==+*#%%@@@@@@@@@@@@@@@@%@@@@@#+%#**=.    .   :---:...   
+==+**+=---=+#+--:--::-::::+#%%@@@#                            .:*#%%%%@@@@@@@@@##%%%@%@@@@@%%%%%@@@@@@@@@@@@@@@@@@@%@@@@@%%%%+*#+=      .-:::..-=:   
++***===--=+#*-----:::--::-#%%%%@@+                        ...:=:.*%%@@@@@@@@@@#*+***#%%%@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@%%@@%%##*-       ..:+*=.    
+***+===-=+*+-----:----=+*##%%%%@%:                      .:::===:.=##%@@@@@@@%#*+++++***##%@%@%@@@@@@@@@@@@@@@@@@@@@@@@@@@%@@@@@%%#*-:.. .    .:.     
***=-=+==**=------====*####%%%%%%+                     .-==---....=*#%%@@@@@@%*++===++****##%%%%%%%@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@%%%*-:.:.            
#*=-=+++*+-===--===+*%@@@@@@@%%%#*                               .+*#%%%@@@@#**+======++**######%%@@@%%%%@@@@@@@@@@@@@@@@@@@@@@@@%###+:-:..           
*-:-**++====---==+*%%%@@@@%%%%%##*:                           ....=###%@@%%#+======+++******###%%@%####%@@@@@@@@@@@@@@@@@@@@@@@@@@#++*-::-...         
-:-***++===----=+#%%@@@%%%@@%#####+.                      ....... .+#%%%%#*+=----=+********#**#%%#**#%@@@@@@@@@@@@@@@@@@@@@@@@@@%%##=+*:::.::         
:=###*+++======*#%@@@%%########%%%*.                       ....   .=#%@#++========++++++++++*%%%%%@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@%%%#*-++::::     .    
=%%****+++++++#%%@%%%%######%%%%%#*:                             .=%%#+++++==+++**++====+**#@@@@@@@@@@@@@@@@%@@@@@@@@@@@@@@@@@@@@@%%%*+#*-:.. ...     
@%#***++***+*#%##%@%#%%%##%%%%%%%#*:                            .-#%#*+++++****+=====+***#%@@@@@@@@@@@@@@@@%%@@@@@@@@@@@@@@@@@@@@@%%%%#%%+......      
@@@##******#%%*:=%%%%%##%%%%%%####+:                             :***+=======--==++*##%@@@@@@@@@@@@@@@@@@@%*%@@@@@@@@@@@@@@@@@@@@@@@@%%%%%=:...       
#%%%%##***#%%=..+%%%%%%%%%%%#%%%@%#+-.                           .-+==--=---==+*#%%@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@%@@@@%%%%%*:       .
++*%%%###%%#:...+%%%%%%%%%%#%%@@@@%=....                          .:-===++*#%@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@%@%##%%%%@@%-.   :+=
-==*%%%%%%+....:*%%%%%%%%%%%@@@@@%+:::.....                        ..-+#%@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@%#****#@@%:...:+=-
+=-=*%%%%=.....*%@@@@%%%@%@@@@@@%: .:::....:.                      .. .=%@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@*=+*=--++....==-=
====-+%#-.....*%%%%@@%%@@@@@@@@@%. ....::::---:.                   .::..:+%@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@#*+==--::-:::=--++
=-=---=-:.  :#%%#%%%%@@@@@@@@@@@+ ....   .:-----:                   .::...-*@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@%*====------:+=-++-
%%%##*++-:.:#%%%%%%@@@@@@@@@@@@#.......      ....                    .:::..-#@@@@@@@@%++%@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@%*=+=--------++:+-.:
@@@@@@@%+:-#%%%%@@@@@@@@@@@@@@@=:::......... .                       ..:::..-#%%%%%%%@*:.+@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@%=--+-------:++:+=...
%%%%%%%#++%%@@@@@@@@@@@@@@@@@@%#=---::....::::                         ..::::*%@@+........-@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@#=--==------:+*:+=....
%%%%%%%%@@@@@@@@@@@@@@@@@@@%+:.  .:-==---:--==:....   ...               ..::-=#@@@@@%#######%@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@*-===------::=*:++.....
@@@@@@@@@@@@@@@@@%%%@@@@@@%+-    .:-===========-:.:-:.:::::::..........:::::-+@@@@@@@%####%@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@*-==-------::=*:++......
@@@@@@@@@@@@@@@%%%@@@@@@@%#+:     :-========-::...:-==----::::::.::::::...::--#@@@@@@@@%@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@*----------::-*-+*:......
@@@@@@@@@@@@%%%@@@@@@@@@@@#=       .::---==--:......::-----::-:::::::::::..:---%@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@*----------::-*-+#+--:::..
@@@@@@@@@@@@@@@@@@@@@@@@@@*         .:----:::.......:....:::::-:------------=+=*@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@#-------=--:::+**#%#+#*==--
@@@@@@@@@@@@@@@@@@@@@@@@@*         ... ...:::.:.:::-::....   ...::-==-----====-*@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@#---------:::--+##*=-==+*+*+
@@@@@@@@@@@@@@@@@@@@@@@%+.         ..   ...:::::::::::..:.        .::::::::--++%@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@#---------:::::--+#=-==++*+-=
@@@@@@@@@@%@@@@@@@@@@@%#=.         .     ..::-:::---::......     ..:....:::=+#%@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@*-----==--:::::::--+++++++**+-
@@@@@@@@@@@@@@@@@@@@@@@%+.         .     .::--:::-------::...........::::-=+%@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@#=---====--:::::------=+#*+++*#+
@@@@@@@@@@@@@@@@@@@@@@@%=.        .      ..:-::::::---===--:::....:::-==+*#@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@#-:::---=-----::-----------=+++*#*
@@@@@@@@@@@@@@@@@@@@@@@%+:        .      ..::::::::::---==+==--::--====*@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@%-..:::::--:----:---------:--==+=+*#
@@@@@@@@@@@@@@@@@@@@@@%%+......  ..      ..:::::::::::-==+++====+*#%@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@%+--:::::+*++=----------------=+=*++#
@@@@@@@@@@@@@@@@@@@@@@%*:..........      ..::::::::::::::::::::=#@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@%*=====+++++++*++++==----------==+*==+*
@@@@@@@@@@@@@@@@@@@@@@%#*-........       ..:---:::::::::::::::::-+#@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@%+-::--=====++++++***##*==-----=+=*+==+*#
@@@@@@@@@@@@@@@@@@@@@@@@*-:.......        .:----:::::::::::::::::-=+#@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@*::::::::----=====+++***=+**+====+*===+*#%
@@@@@@@@@@@@@@@@@@@@@@@@%*--::::..        .--=------::::::::::----=+#@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@+::::::::::::---======++==+++++==*+==++#%%%
@@@@@@@@@@@@@@@@@@@@@@@@@@%*+-:::.        ......::::::::::-------=*%@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@#:::::::::::::::::---=====+++===+*+==+*#%%%@
%%@@%@@@@@@@@@@@@@@@@%@@@@@%%**+-.        .        .:::::::-----=*@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@%-:::::::::::::::::::----====++=**+++**#%%%@@
#@%######%@@%%*==--===-=====+=+++=-:..   ..     ....::.....::-=#@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@%-::::::::::::::::::::::----===+#+++*##%%%%@@@
%#**##*#%%#%%#+=---=====+**=---------===-::::.  .:-:-::......:*%@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@#::::::::::::::::::::::-------=**+++###%%%@@@@@
#******#+==+++========+**+-::--====-------=====++=++=-::......-#@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@#=::::::::::::::::::::-:::::::-=*+++*##%%%%@@@@@@
****+--=====------+*###=::-=====---=++==++***+#%%*+#*=-::......-*@@@@@@@@@@@@@@@@@@@@@@@@@@@#+=--===:...::::::::::::::==+==--:::::-**++*###%%%@@@@@@@%
##+:-+=-------=+***+=-:::-=+*++=+++*#@%###*#**++++**#*=-:.......-#@@@@@@@@@@@@@@@@+===-:---::::...........::::::::::--+*#%%#**+-:=*+++*##%%%%@@%%@@@%%
**-=++=++++++++++=-:::--====+**#%%@@@@%#***++**++##**#*+=:.......-#@@@@@@@@@@@@@@@-..........:..............::::::..:==--**-=*%%#*+**##%%%%@%%%%%%@@%%
#=+++===*%@@#+-:::.:--=++===*%@@%%#%%%##%%%#****+*#*#%@##-........-#@@@@@@@@@@@@@#:..:...................::..::::::::===-=#-::=##**##%%%%@@@@@@@@@@%@@
#+**==+*##*=------=--=--==-=#%@%**##%%**#%%%#*##**%@%##*==:........+@@@@@@@@@@@@*:.:......................::::::::::::=+==#+-+#***#%%%%%@@%@@@@@@@@@@@
++++=*#%#=:::--=**+*=:----+###**#*#####*#%%@@@@@@%#%+=+*--:........-%@@@@@@@@@%-.........................:...::::::::::=*+*#****#%%%%%@@%%=*%@@@@%%%%@
====*%%*------=++*+=+=---+#%%#=+*%#***#%%%#*+#%##*++++*#*-:........-%@@@@@@@*-...............................:-*###+::-:-**+*#*#%%%%@@@@#**==%@@@%@@@@
++*###=-----=+=++-===+++####*++++##*#%%@%====#####****+*=-:........-+%@%#+-....................:..........::::+@@@@@@*=--=#%#*#%%%%@@%%###*++-*%@@@@@@


~~~


    ╝  ,                                       ,@▓█▀         └▓███████▓▓█▒W
                                              ▄▓██▌            ▀▀███████▓▓[
    W                            ░.,        ╓▓╫███▌               ╙█████▓█▌
                                     %     ╔▓▓▓███▌                ]████▓▓
    ╚Ñ═   ,,                  ,  ,       ,╫╫╫▓████▓▓▄  ╥▄▄▄▄▄▄▄    ▐██████▌
    m    g`                  ╓  ` ``   ,φ▓╣╫▓█████▀╣▒▌  ▀▓▄`;~     ╟▓█████
     .          `─.         ╓ ▒▒,▒▄,╓╓▓▓▓▓╢▓█████▌        ╙*─    ,φ▓█████▌
            ,╥,            ║ Æ▄▓▓▓▓░▒▒▓▓▓╣▓███████@▌            g▓▓████▓█
       ╓Ñ  @`             ╣,▓█▌▓▓╜╓▒▓▓▓▓▓▓████████▓█▄▄▄@▄,], ╖@╬▒█████▓█▌
      ╢╜ ,▒              ,@█▓█▓▓▒╢▓▓▓▓▓███████████Ü▓█╣║▒╣▓▓▌╓g╢▓████████
    ╓ ╜ ,░       Æ▀`    ╩████╣╣▒▓▓▓███████████████▌▓▒▀▀▀`  ]╢▓█████▓▓█▓▌
    ,╜ ╓    r   ╙╜[ ▄█    ▀▀▀▀▀▀▀████████▓█████████▒╜╝`  ,@▒▓████████▓█▓
    ` ╟╜   ▓       ▓▓█L               ▐████████▓████▄▄▄▄██████▓███▓██▓▐▀▓y       ,
     ╫╜   ╬      ╓▓▓▓█               ╓ ▐▓█████╜ ╙╙╙▓█████▓▓██████████▓█▓▓▓      ╝
    ▒`  ╓▀     ▄███▓▓▓            ``   ]▓████Ñ   "╝╨▒▓▓▓▓██▓█████████████▓▌
     ,╣      ▄▓██▓█▓▒▒,                 ▓▓█▓    am▒,╓Ñ▓▓▀╙▄██████████████▌▌╢
    ,▓     ,▓▓█▓╣▒▒▓▓▓▌                ╒▓▀╧  ,,╓^` ,╥█████████████████████▓▄▄
    █▌┐, `▄█ ▓▓▓▓▓▓▓▓▒Γ                ▀╝^^"`` ,╓▄▄██████████▀████████████▓▓▓▌
    ╜▓▓╣▒▓▀  ▓▓▓▓▓▓▓███`                  ,,▄▄█████████████████████████████▀█▓█▄   ~
      ▀▓█   ▄▓█▓▓▓████                    ▀████████████████████████████████▀╓'▀▀  ▒
           ▓▓▓▓▓█████`                      █████████████████▓████████████▌      F,Ö
    ▓▓██╕ █▓▓▓███████                        ▓█▀▀█.'█████████████████████▀      F╓
    ▓▓▓▓████▓▓▓▓███▀    ,   ╖                ▐██▄@@@▓███████████████████  '    ╝╓
    ████████▓▓████M    '       '«             █████████████████████████       ╝╓
    ████▓▓████████                           *▐▓██████████████████████       ▐φ█▀N,
    █████████████                            µ███▓▓██████████████████         ▓  ┌▓
    █████████████                         ,,▄████▓▓████████████████▀           'Ñ  ▓
    ████████████▓                  ╖. ,╓▄▄██████▓████████████████▀   ╓,           ¿╠
    ████████████▌                     ▀████████████████████████▀    ▒ ' ╢N▄      ' ╓
    █████████████,                      ▐████████████████████▀           ╙  ▒H /  g▓
    ██████████████▓▄,                  ▄█████████████████████                ,` ╓▓▓█
    █╩Ñ╩╬█▓▀    ─▄⌐^""` ,            ╔█████████████████████▀                * ╓▓▓▓██
    `╨╝".-" "`▄▄╝   -  ╖ ,,,▄▄╕Z      ████████████████▀▀▀▀         ,,     ¿ ,╣▓▓████
    ▓ Æ,─,,╥Ñ▀`   `╓╖▄▄██▓[`,,Φ▒╥╖     █████████                    ▀▀▀&▄Ç`@▓▓█▓▓▓█▓
    L[ ,▓▓▀  , ⌐   ██▀▒▓╨▓▓▓d,▄▄█▀t     ███████                    ╖╖▓ Æ/g▓▓▓███████
      ▄▓▀   ^⌐ , ,▓▓ ▐@▒]▓▀▀▀▀▀  ╔      ▐████▀                 ▄▄▄  ╙,╢N▓▓▓██▓ ██▓█▓
    ╩▀╜   ╝╚`*╜╩▀▀▀╝╩╩╩▀▀▀╜╘▀▀▀╩╨╨       `                  ═══▀▀▀▀*─▀▀╩▀▀▀▀▀▀╝═╚▀▀▀
   
