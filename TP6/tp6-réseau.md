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

    Maintenant il faut partager les routes sur les 5 routeurs en indiquant les zones dans lesquels ils sont selon le réseau partagé :

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

        `network 10.6.100.8 0.0.0.3 area 1`

    * R4 :

        `network 10.6.100.4 0.0.0.3 area 0`

        `network 10.6.100.12 0.0.0.3 area 0`