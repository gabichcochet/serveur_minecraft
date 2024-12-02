# serveur_minecraft : Byte Builder

### PARTICIPANTS : WILLIAM KROMMER, GABRIEL COCHET, NORTHON KOFFI, RODNEY NGUEMA BEKUI, RAPHAEL GEAY, HUGO AGUER

### BUT : créer un serveur minecraft de survie accessible à tous (version Java) 

### Fonctionalités : 

limite 5-12 joueurs  

accessible par java 

serveur moddé 

mode : pré-définit survie mais modifiable 

difficulté : pré-définit normale mais modifiable

sécurité du serveur (hébergeur du serveur) = contabo 

plugins : par exemple pour empecher que le serveur lag(clearlag)

### Surveillance des logs

système des rotations des logs

vérifier les logs d'erreurs

monitorer les syslog et logwatch

donner la possibilité d'avoir un worldbackup
à l'aide de Rcon

### Configuer le firewall le port forwarding

authoriser le port sur lequel le serveur fonctionnera

Dépendant de la zone géographique choisit: France 

on utilisera soit Firewall soit GeoIP pour assurer l'accés uniquement d'utilisateurs
vivant en France 

### génération du monde 

créer un fichier qui authorise les utilisateur a choisir la seed/graine du monde qu'ils désirent
à l'aide de modifications dans le fichier "server.props" / ou faire un plugin ( à l'aide d'une commande accessible que dans le monde
pre-gen)

Implementer une API : Paper (améliorer la performance graphique)

Etendre la RAM du serveur
















