# B1 Réseau 2018 - TP4
Thomas DUMONT

## I. Mise en place du lab
### 2. Création des VMs

Il suffit de modifier le fichier "ifcfg-enp0s3" pour pouvoir donner une adresse IP à la carte réseau dans la VM cliente, serveur et routeur et ainsi pouvoir communiquer avec net1 ou net2.
Pour la VM routeur il faudra créer un fichier appelé "ifcfg-enp0s8", et configurer son adresse IP, dans le même dossier que "ifcfg-enp0s3" pour qu'elle puisse communiquer avec les deux réseaux host-only.

### 3. Mise en place du routage statique

* 1. VM routeur :

    Après avoir effectué la commande `ip route show`, on peut constater qu'il y a bien des routes pour aller vers net1 et net2 :
    ![](https://i.ibb.co/D7d0Dsr/iprouteshow-centos-routeur.png)

* 2. VM client :

    Faire en sorte que la machine ait une route vers net1 et net2 :
    La route vers net1 est déjà configuré pour la VM cliente. Il faut alors configurer la route vers net2.

    Pour configurer la route vers net2, il faut passer par la VM routeur.
    
    Pour cela :
    aller dans le dossier "network-scripts", créer un fichier "route-enp0s3" et écrire à l'intérieur : `10.2.0.0/24 via 10.1.0.254 dev enp0s3`.
    Cela veut dire qu'on veut accèder au réseau 10.2.0.0/24 par 10.1.0.254 qui est l'adresse IP du routeur grâce à la carte réseau enp0s3.

    Après un `ip route show` on peut constater que ça a fonctionné :
    ![](https://i.ibb.co/kmDRnYV/iprouteshow-centos-cliente.png)

* 3. VM serveur :

    Même principe que pour la VM client.
    On créer un fichier "route-enp0s3" et on écrit à l'intérieur : `10.1.0.0/24 via 10.2.0.254 dev enp0s3`.

* 4. Test :

    Client1 peut effectivement ping server1 et inversement.

    Après avoir effectué la commande `traceroute` sur la VM cliente vers la VM serveur, voici le résultat :

    ![](https://i.ibb.co/FsGC7vk/traceroute-client-to-serveur.png)

## II. Spéléiologie réseau
### 1. ARP
#### A. Manip 1

* VM Client :

    Expliquer la seule ligne visible dans la table ARP :

    ![](https://i.ibb.co/g4NWtdv/ip-neigh-show-client.png)

    Cette ligne s'affiche car l'hôte est connecté en ssh à la VM client, il y a donc un échange de données. La VM connait alors l'adresse mac de la carte réseau avec laquelle elle communique et affiche "reachable" qui signifie "accessible", on peut donc échanger des données. L'adresse IP "10.1.0.1" est net1, on passe par là pour se connecter à la VM en SSH.

* VM Serveur :

    Expliquer la seule ligne visible dans la table ARP : 

    ![](https://i.ibb.co/qJWV4df/ip-neigh-show-serveur.png)

    Même chose que pour la VM client, on est connecté en ssh à la VM serveur,cette ligne s'affiche alors avec l'adresse mac de la carte réseau avec laquelle elle communique.

* VM Client :

    Expliquer le changement dans la table ARP après avoir ping la VM serveur :
    
    ![](https://i.ibb.co/nb4kyQM/ip-neigh-show-client-2.png)

    Après avoir ping la VM serveur, une nouvelle ligne s'affiche dans la table ARP avec l'adresse IP ainsi que l'adresse mac de net1 car l'on doit d'abord passer en premier par cette carte réseau avant d'atteindre la VM serveur.

* VM serveur :

    Expliquer le changement dans la table ARP après avoir ping la VM serveur :

    ![](https://i.ibb.co/R3QvmZf/ip-neigh-show-serveur-2.png)

    Après que la VM client a ping la VM serveur, une nouvelle ligne s'affiche dans la table ARP avec l'adresse IP ainsi que l'adresse mac de
    net2 car c'est la dernière carte réseau qui fait passer le message avant d'atteindre la VM serveur.

#### B. Manip 2

* VM routeur :

    * Expliquer la/les ligne(s) :

    ![](https://i.ibb.co/cynHZCf/ip-neigh-show-routeur.png)

    Cette ligne s'affiche car l'hôte est connecté en ssh à la VM routeur, il y a donc un échange de données. La VM connait alors l'adresse mac de la carte réseau avec laquelle elle communique et affiche "reachable" qui signifie "accessible", on peut donc échanger des données. L'adresse IP "10.1.0.1" est net1, on passe par là pour se connecter à la VM en SSH.

    * Expliquer les changements sur la VM routeur après avoir ping la VM serveur depuis la VM client :

    ![](https://i.ibb.co/WchvRj6/ip-neigh-show-routeur-2.png)

    Deux nouvelles lignes se sont affichés, l'une affiche l'adresse ip ainsi que l'adresse mac de la carte réseau qu'utilise la VM client pour communiquer et l'autre l'adresse mac et l'adresse ip qu'utilise la VM serveur pour communiquer. Elles s'affichent dans la table ARP de la VM routeur car pour que la VM client et la VM serveur communique, elles doivent passer par la VM routeur. La VM client a donc dans un premier temps ping la VM serveur, une première ligne s'est alors affichée dans la table ARP, et en retour la VM serveur a envoyé une réponse à la VM client, une deuxième s'est alors affichée.

#### D. Manip 4

Expliquer quelle machine porte l'IP qui vient de pop dans la table ARP :

![](https://i.ibb.co/cFNXPN5/ip-neigh-show-client-3.png)

La carte réseau enp0s3 ayant comme IP 10.0.2.15 (obtenue après un ip a) nous permet d'accéder à internet, l'adresse IP 10.0.2.2 est alors l'adresse IP de la carte NAT.
