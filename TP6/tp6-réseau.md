# B1 Réseau 2018 - TP5
Thomas DUMONT

## Lab 2 : Un peu de complexité (et d'utilité ?...)

On parle de `r1.tp6.b1`, `r2.tp6.b1`, `r3.tp6.b1`, `r4.tp6.b1` et `r5.tp6.b1` :
* [X] Définition des IPs statiques
* [X] Définition du nom de domaine

### Checklist VMs

On parle de `client1.tp6.b1`, `client2.tp6.b1` et `server1.tp6.b1` :
* [X] Désactiver SELinux
  * déja fait dans le patron
* [X] Installation de certains paquets réseau
  * déja fait dans le patron
* [X] Enlever la carte NAT
  * utiliser uniquement la carte de GNS3
* [X] Définition des IPs statiques
* [X] Définition du nom de domaine
* [X] Remplir les fichiers hosts

### Configuration de OSPF

* Sur tous les routeurs :

    Passer en mode configuration dans la console du routeur, taper la commande `router ospf 1` pour activer l'OSPF et leurs donner l'id 1.1.1.1 en tapant `router-id 1.1.1.1`.

* Partage de routes :

    Maintenant il faut partager les routes sur les 5 routeurs en indiquant les zones dans lesquels ils sont selon le réseau partagé en effectuant les commandes suivantes :

    * R1 :

        `network 10.6.100.0 0.0.0.3 area 0`

        `network 10.6.100.4 0.0.0.3 area 0`

        `network 10.6.202.0 0.0.0.255 area 2`

    * R2 :

        `network 10.6.100.0 0.0.0.3 area 0`

        `network 10.6.100.8 0.0.0.3 area 0`

    * R3 :

        `network 10.6.100.12 0.0.0.3 area 0`

        `network 10.6.100.8 0.0.0.3 area 0`

        `network 10.6.101.0 0.0.0.3 area 1`

    * R4 :

        `network 10.6.100.4 0.0.0.3 area 0`

        `network 10.6.100.12 0.0.0.3 area 0`

    * R5 :

        `network 10.6.101.0 0.0.0.3 area 1`

        `network 10.6.201.0 0.0.0.255 area 1`

* Vérifier que ça fonctionne :

    * `show ip route`:

        Pour le router1 il est possible d'obtenir ce résultat :

        ![](https://image.noelshack.com/fichiers/2019/11/5/1552684872-show-ip-route.png)

    * `show ip protocols` :

        ![](https://image.noelshack.com/fichiers/2019/11/5/1552685581-show-ip-protocols.png)

    * `show ip ospf neighbor` :

        ![](https://image.noelshack.com/fichiers/2019/11/5/1552685089-show-ip-ospf-neighbor.png)

    * ping r1.tp6.b1 --> r5.tp6.b1 :

        ![](https://image.noelshack.com/fichiers/2019/11/5/1552685289-ping-r1tor5.png)

    * ping client1.tp6.b1 --> server1.tp6.b1 :

        ![](https://image.noelshack.com/fichiers/2019/11/5/1552685412-ping-client1toserver1.png)

    * `traceroute client2` depuis le server1 :

        ![](https://image.noelshack.com/fichiers/2019/11/5/1552685968-traceroute-server1toclient2.png)

## Lab 3 : Let's end this properly

### 1. NAT : accès internet

Après avoir configuré le router4 pour qu'il ai accès à internet et qu'il puisse donner accès à internet à toutes les VMS, on peut effectivement confirmer le bon fonctionnement en observant les résultats suivant :

* client2 :

    ![](https://image.noelshack.com/fichiers/2019/11/6/1552767216-client2-ping-8-8-8-8.png)

* router2 :

    ![](https://image.noelshack.com/fichiers/2019/11/6/1552767361-r2-ping-google-com.png)

### 2. Un service d'infra

Pour pouvoir accèder à internet depuis les VMS client1, client2 et server1 il a fallu que j'aille dans le dossier "/etc/resolv.conf" et que j'ajoute la ligne `nameserver 8.8.8.8`.

* server1 :

    Après le lancement du serveur web et un curl localhost, on peut effectivement constater que le serveur fonctionne :

    ![](https://image.noelshack.com/fichiers/2019/11/7/1552837769-server1-curl-localhost.png)

* client1 :

    Après un `curl server1.tp6.b1`, je peux effectivement accèder au serveur qui est sur la VM server1 :

    ![](https://image.noelshack.com/fichiers/2019/11/7/1552837880-client1-curl-server1.png)

### 3. Serveur DHCP

Configuration du fichier dhcpd.conf dans la VM client1 : 

![](https://image.noelshack.com/fichiers/2019/11/7/1552836541-dhcpd-conf-client2.png)

Suite à l'activation du DHCP dans la VM client1 ainsi qu'à la configuration de l'interface réseau en DHCP dans la VM client2, on peut effectivement constater que l'IP du client2 est bien dans la plage d'IP définie dans dhcpd.conf :

![](https://image.noelshack.com/fichiers/2019/11/7/1552836726-dhcp-client1-ip.png)

### 4. Serveur DNS

Après avoir mis en place le serveur DNS sur la VM server1, voici quelques test pour confirmer qu'il fonctionne correctement :

* dig server1.tp6.b1 :
    ![](https://image.noelshack.com/fichiers/2019/11/7/1552843471-dig-server1.png)

* dig client2.tp6.b1 :

    ![](https://image.noelshack.com/fichiers/2019/11/7/1552844246-dig-client2.png)

* dig -x 10.6.201.10 :

    ![](https://image.noelshack.com/fichiers/2019/11/7/1552844316-dig-x-10-6-201-10.png)