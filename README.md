# serveur_minecraft : Byte Builder

### PARTICIPANTS : WILLIAM KROMMER, GABRIEL COCHET, NORTHON KOFFI, RODNEY NGUEMA BEKUI, HUGO AGUER

### BUT : créer un serveur minecraft de survie accessible à tous (version Java) 

### Fonctionalités : 

limite 5-12 joueurs  

accessible par java 

serveur moddé/plugin/vanilla

mode : pré-définit survie 

difficulté : pré-définit normale 

sécurité du serveur (hébergeur du serveur) = contabo 

plugins : par exemple pour empecher que le serveur lag(clearlag)

##### partitionement 
##### étant donner que notre serveur avait plusieurs part de blocks de 5 Go, 7 GO et un de 380 Go, tout avait été fait sur un /minecraft

```
root@vmi2336822:/mnt/minecraft_forge# lsblk
NAME    MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
loop0     7:0    0 682.9M  1 loop /usr/lib/live/mount/rootfs/filesystem.squashfs
sda       8:0    0   400G  0 disk
├─sda1    8:1    0   399G  0 part /mnt
├─sda14   8:14   0     4M  0 part
├─sda15   8:15   0   106M  0 part
└─sda16 259:0    0   913M  0 part
```

```
sudo mount -a
```

### sécuriser le serveur à l'aide de iptables et fail2ban

###### permet la connexion entre le seveur de communiquer avec lui même

``` 
iptables -A INPUT -i lo -j ACCEPT
iptables -A OUTPUT -o lo -j ACCEPT
```

###### permet les utilisateurs connectés de rester en connexion ssh

```
iptables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT
```

###### permet le traffic de données envers le port du(des) serveurs Minecraft

```
iptables -A INPUT -p tcp --dport 25565 -j ACCEPT
```

###### pour authoriser seulement la connexion entre une ou plusieurs addresses iptables

```
iptables -A INPUT -p tcp --dport 25565 -s 198.7.127.5(par exemple) -j ACCEPT
```

###### pour bloquer le traffic entrant

```
iptables -A INPUT -j DROP
```

##### place la connexion ssh devant DROP

```
sudo iptables -I INPUT 4 -p tcp --dport 22 -j ACCEPT
```


```
root@vmi2336822:~# sudo iptables -L
Chain INPUT (policy ACCEPT)
target     prot opt source               destination
ACCEPT     tcp  --  anywhere             anywhere             tcp dpt:ssh
ACCEPT     tcp  --  198.7.127.5         anywhere             tcp dpt:25565
ACCEPT     all  --  anywhere             anywhere             ctstate RELATED,ESTABLISHED
DROP       all  --  anywhere             anywhere

Chain FORWARD (policy ACCEPT)
target     prot opt source               destination

Chain OUTPUT (policy ACCEPT)
target     prot opt source               destination
ACCEPT     all  --  anywhere             anywhere
```

###### installer fail2ban

```
apt install fail2ban
```

###### configuration du fail2ban

```
nano /etc/fail2ban/jail.d/minecraft.conf
```

```
[minecraft]
enabled = true
port = 25565
filter = minecraft
logpath = /path/to/your/minecraft/logs/latest.log
maxretry = 5
bantime = 3600  # Ban time in seconds (1 hour)
findtime = 600  # Time window to track failed attempts (10 minutes)
action = iptables[name=Minecraft, port=25565, protocol=tcp]

```

```
nano /etc/fail2ban/filter.d/minecraft.conf
```

```
[Definition]
failregex = ^.*\[.*\] .*(Invalid IP address).*$
ignoreregex = ^.*\[.*\] .*(Invalid IP address: 192.168.1.100).*$

```

```
nano /etc/fail2ban/jail.local
```

```
[invalid-ip-address]
enabled = true
filter = invalid-ip-address
action = iptables[name=invalid-ip-address, port=ssh, protocol=tcp]
logpath = /path/to/your/logfile.log
maxretry = 3
bantime = 3600
findtime = 600
```

###### reboot le fail2ban

```
systemctl restart fail2ban
```

```
systemctl enable fail2ban
```

```
fail2ban-client status minecraft
```

```
Status for the jail: minecraft
|- Filter
|  |- Currently failed: 0
|  |- Total failed: 3
|  |- File list:    /path/to/your/minecraft/logs/latest.log
|
|- Actions
|  |- Currently banned: 1
|  |- Total banned: 1
|  `- Banned IP list:   192.168.1.100
```

### installation du(des serveurs)

#### setup

##### pour minecraft vanilla

###### créer une directory "minecraft"

```
mkdir minecraft
``` 

##### télécharger java (dépendant de la version dans la quelle minecraft se trouve(java 7, 8, 17 etc))

``` 
apt install openjdk-17-jdk -y
```

##### vérifier la version 

```
java -version
```

##### si jamais vous avez installer plusieurs versions vérifier les avec :

```
update-alternatives --config java
``` 

##### créer un fichier eula.txt pour activer le fonctionnement du jeu (cela sera nécéssaire pour forge et paper aussi)

```
touch eula.txt; echo "eula=true" > eula.txt
```

```
mv eula.txt minecraft
```

##### télécharger le serveur vanilla

###### rendez-vous sur ce lien :

https://mcversions.net/

###### télécharger le serveur paper (plugins)

```
https://api.papermc.io/v2/projects/paper/versions/1.21.4/builds/107/downloads/paper-1.21.4-107.jar
```
###### pour le lancer ce sera la même commande que pour le serveur vanilla ci dessous, sauf que vous remplacerez le "server.jar" par le nom du serveur.

###### télécharger le serveur forge (moddé)

```
wget https://maven.minecraftforge.net/net/minecraftforge/forge/1.20.6-50.1.32/forge-1.20.6-50.1.32-installer.jar
```

```
java -jar forge-1.20.6-50.1.32-installer.jar --installServer
```

###### pour le lancer :

```
./run.sh
```

###### vous pouvez toujours changer de version en reprenant le lien de téléchargement d'autre versions :

pour forge :  https://files.minecraftforge.net/net/minecraftforge/forge/

pour Paper :  https://papermc.io/downloads/paper  
(pour télécharger les plugins/mods vous utiliserais la commande scp(en dehors de linux, sur powershell) en prenant la directory du fichier plugins/mods)

###### entrez cette commande pour télécharger le serveur avec la version trouvé(en copiant le lien de téléchargement sur le site dessus à la version):

###### (ici avec la version 1.21.4 pour exemple)

```
wget https://piston-data.mojang.com/v1/objects/4707d00eb834b446575d89a61a11b5d548d8c001/server.jar
```

#### lancer le serveur a l'aide de cette commande (vous pouvez ajuster le nombre de Gigabytes au minimum et maximum selon la puissance de votre pc)

```
java -Xmx1G -Xms1G -jar server.jar nogui
```

Enfin pour accéder au serveur sur minecraft écrivez dans l'addresse du server 

```
198.7.127.5:25565
```





