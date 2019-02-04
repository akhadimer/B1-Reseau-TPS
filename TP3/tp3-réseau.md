# B1 Réseau 2018 - TP3
Thomas DUMONT

## I. Création et utilisation simples d'une VM CentOS
### 4. Configuration réseau d'une machine CentOS
* a. Utilisez une commande pour prouver que vous avez internet depuis la VM :
Utilisation de la commande "ping" ayant comme cible "www.google.fr".

    Résultat : 
![](https://i.imgur.com/aymdXYl.png)

    L'accès à internet depuis la VM est donc possible.

* b. Prouvez que votre PC hôte et la VM peuvent communiquer :

    A partir du moment où la machine virtuelle à accès à internet, ça veut dire que l'hôte lui fournit internet et donc que les deux peuvent communiquer.

* c. affichez votre table de routage sur la VM et expliquez chacune des lignes :

    Faire la commande : 
    > route

    ![](https://i.imgur.com/YRohxtB.png)
    "Destination" : C'est une adresse IP qui indique quels sont les paquets de données qui vont suivre cette route selon leur destination.
    "Passerelle" (gateway) : C'est une adresse IP qui indique par où les paquets vont passer pour arriver à destination. Ils seront envoyés à cette adresse.

    "Genmak" (masque de sous-réseau) : c'est une suite de 4 octets (comme une adresse IP) qui permet d'indiquer quelle est la taille de chaque partie de l'adresse IP (partie réseau et partie hôte).

    "Indic" (indicateurs) : Correspond à l'état de la route (U signifie que la route est active (Up) et G que la route est une passerelle (Gateway)).

    "Metric" (sauts) : C'est un nombre qui indique combien d'intermédiaires il reste avant d'arriver à la destination.

    "Ref" (références) : C'est un nombre qui indique le nombre de références associées à cette route.

    "Use" (utilisation) : C'est un compteur d'utilisation de la route.

    "Iface" (interface réseau) :  C'est le nom de l'interface réseau qui sera utilisée pour cette route.

### 5. Faire joujou avec quelques commandes

* ping hôte -> VM :

    ![](https://i.imgur.com/3vUJzHD.png)

* ping VM -> hôte :

    ![](https://i.imgur.com/qE3Rq7q.png)

* afficher la table de routage de l'hôte :

    Commande à effectuer : 
    > route print -4

    ![](https://i.imgur.com/b0nf1Fk.png)

* afficher la table de routage de la VM :

    Commande à effectuer : 
    > route -4

    ![](https://i.imgur.com/QsuY5ce.png)

* depuis la VM utilisez curl (ou wget) pour télécharger un fichier sur internet :

    Commande effectué : 
    > curl -o website https://curl.haxx.se

    ![](https://i.imgur.com/TIVMvnM.png)

* depuis la VM utilisez dig pour connaître l'IP de :
    * ynov.com :

    commande effectué : 
    > dig ynov.com

        ![](https://i.imgur.com/TAhLsTW.png)
    Résultat : 217.70.184.38

    * google.com :

    commande effectué : 
    > dig google.com

        ![](https://i.imgur.com/nlmDNpj.png)
    Résultat : 216.58.201.238

## II. Notion de ports et SSH
### 1. Exploration des ports locaux

* utilisez la commande ss pour lister les ports TCP sur lesquels la machine virtuelle écoute :

    Commande effectué :
    > ss -l -4 -n -p -t

    -l permet d'afficher les interfaces de connexion qui écoute

    -4 permet d'afficher seulement les interfaces de connexion qui utilise le protocole IPV4

    -n permet d'avoir un numéro de port plutôt qu'un nom

    -p permet de connaître l'application qui écoute sur ce port

    -t permet d'afficher seulement les interfaces de connexion avec un port TCP

    ![](https://i.imgur.com/d5wyRxz.png)

### 2. SSH

* connectez-vous en SSH à la machine virtuelle :
    Il suffit de taper la commande suivante dans le cmd ou le powershell de windows :
    > ssh root@192.168.127.10

### 3. Firewall
## A. SSH

* vérifiez que votre serveur SSH écoute sur un port différent de 22 (le vôtre) :
    ```
    [root@localhost ssh]# ss -l -n -4 -t
    State     Recv-Q  Send-Q    Local Address:Port   Peer Address:Port
    LISTEN    0       100         127.0.0.1:25              *:*
    LISTEN    0       128                 *:2048            *:*
    ```
    Mon serveur SSH écoute bien sur le port que j'ai moi même choisi.

* sans autre modification, la connexion devrait échouer
    * Expliquez pourquoi :
        Les connexions sur le port TCP 2048 n'ont pas était autorisés.
    * trouvez une solution :

        Il suffit de faire les commandes suivante :
        > firewall-cmd --add-port=2048/tcp --permanent

        > firewall-cmd --reload

        Il sera alors possible de se connecter au serveur grace au client SSH.

## B. netcat

* Depuis un premier terminal sur la VM :
    Pour lancer un serveur netcat sur le port 5454 effectuer cette commande :
        
    > nc -l 5454

* Dans un deuxième terminal sur l'hôte :
    Pour se connecter au serveur netcat suivre ce procéder :
        Lancer le powershell, se placer dans le dossier netcat téléchargé plus tôt, lancer "nc64.exe", il sera affiché la chose suivante :"Cmd line:", il suffira de taper l'adresse ip de la machine suivi d'un espace et le port. Cela donne :

    > Cmd line: 192.168.127.10 5454

* Dans un troisième terminal sur la VM :
    Effectuer cette commande pour visualiser la connexion netcat en cours :
    > ss -l -n -4 -t

    Résultat :

    ```
    State     Recv-Q Send-Q  Local Address:Port     Peer Address:Port
    LISTEN    0      10                  *:5454                *:*
    LISTEN    0      100         127.0.0.1:25                  *:*
    LISTEN    0      128                 *:2048                *:*

    ```
## III. Routage statique
### 1. Préparation des hôtes
#### Préparation VirtualBox

Sur le PC1, l'adresse IP du réseau host-only qui lui a était attribué est : 192.168.101.1/24 et sur le PC2 : 192.168.102.1.

Les adresses IP sur les VMS ont étaient changé dans etc/sysconfig/network-scripts/ifcfg-enp0s8

#### Check
PC1 et PC2 arrivent bien à se ping.
VM1 et PC1 peuvent se ping.
VM2 et PC2 arrivent aussi à se ping.

Table de routage :
Après observation, on peut remarquer que l'ip de la carte ethernet et du host-only configuré plus tôt s'affiche dans la table de routage.

### 2. Configuraton du routage

Faites un bilan des adresses IP portés par chacune des interfaces utilisées dans le TP, pour les 4 machines (PC1, PC2, VM1, VM2) :

* Carte Ethernet :  
    * PC1 : 192.168.112.1/30
    * PC2 : 192.168.112.2/30
* Host-only :
    * PC1 : 192.168.101.1/24
    * PC2 : 192.168.102.1/24
* enp0s8 :
    * VM1 : 192.168.101.10/24
    * VM2 : 192.168.102.10/24

#### PC1
Commande effectué depuis Windows : 
> route add 192.168.102.0/24 mask 255.255.255.0 192.168.112.2

Après avoir tenté de ping l'adresse IP du PC2 dans le réseau 2, depuis le powershell sur le PC1, le résultat obtenue est une réussite.

![](https://i.imgur.com/OCaj5hx.png)

#### PC2
Commande effectué depuis Windows : 
> route add 192.168.101.0/24 mask 255.255.255.0 192.168.112.1

Après avoir tenté de ping le PC1 dans le réseau 1 depuis le PC2, le résultat est aussi une réussite.

#### VM1
Commande effectué depuis CentOS : 
> ip route add 192.168.112.0/30 via 192.168.101.1 dev enp0s8

Après avoir tenté de ping l'adresse du PC2 dans le réseau 12 ainsi que l'adresse du PC2 dans le réseau 2 depuis la VM1, le résultat est une réussite.

![](https://i.imgur.com/FXxZCBa.png)

#### VM2
Commande effectué depuis CentOS : 
> ip route add 192.168.112.0/30 via 192.168.102.1 dev enp0s8

Après avoir tenté de ping l'adresse du PC1 dans le réseau 12 ainsi que l'adresse du PC1 dans le réseau 1 depuis la VM2, le résultat est aussi une réussite.

### 3. Configuration des noms de domaine
