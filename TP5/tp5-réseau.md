# B1 Réseau 2018 - TP5
Thomas DUMONT

## II. Lancement et configuration du lab

* Checklist IP routeurs :
    * Routeur 1 :

        Pour définir une IP au routeur1 sur la carte ethernet qui est connecté au serveur1 il suffit d'aller sur GNS3, de cliquer sur start pour allumer le routeur, ouvrir solar-putty en double cliquant sur l'image du routeur et taper les commandes suivantes : 

        ```
        # conf t
        (config)# interface ethernet 0/1
        (config-if)# ip address 10.5.1.254 255.255.255.0
        (config-if)# no shut
        ```

        Pour définir l'adresse IP sur la carte ethernet du routeur1 qui est connecté au routeur2 :

        ```
        # conf t
        (config)# interface ethernet 0/0
        (config-if)# ip address 10.5.3.1 255.255.255.252
        (config-if)# no shut
        ```

    * Routeur 2 :

        Pour définir l'adresse IP sur la carte ethernet du routeur2 qui est connecté au routeur1 :

        ```
        # conf t
        (config)# interface ethernet 0/0
        (config-if)# ip address 10.5.3.2 255.255.255.252
        (config-if)# no shut
        ```

        Pour définir l'adresse IP sur la carte ethernet du routeur2 qui est connecté au switch :
        
        ```
        # conf t
        (config)# interface ethernet 0/1
        (config-if)# ip address 10.5.2.254 255.255.255.0
        (config-if)# no shut
        ```

* Checklist routes :

    * Routeur 1 :

        Route : ip route 10.5.2.0 255.255.255.0 10.5.3.2

    * Routeur 2 :

        Route : ip route 10.5.1.0 255.255.255.0 10.5.3.1

    * Serveur 1 :

        Route : 10.5.2.0/24 via 10.5.1.254 dev enp0s3

    * Client 1 :

        Route : 10.5.1.0/24 via 10.5.2.254 dev enp0s3

    * Client 2 :

        Route : 10.5.1.0/24 via 10.5.2.254 dev enp0s3

## III. DHCP
### 1. Mise en place du serveur DHCP

* Renommer la machine

    Aller dans le fichier /etc/hostname et écrire :

    ```
    dhcp-net2.tp5
    ```

* Démarrage du serveur dhcpd sur la vm client2 :

Après avoir effectué la commande ``sudo systemctl start dhcpd``, on peut remarquer que le serveur dhcpd est bien activé avec la commande ``systemctl status dhcpd`` :

![](https://image.noelshack.com/fichiers/2019/09/2/1551217395-result-status-dhcpd.png)

* Faire un test :

La configuration de la carte réseau enp0s8 sur la VM client1 est la suivante :

```
NAME=enp0s8
DEVICE=enp0s8

BOOTPROTO=dhcp
ONBOOT=yes
```

Utilisation de dhclient :
    
* Lacher le bail DHCP :

```
[root@localhost akha]# dhclient -v -r
Internet Systems Consortium DHCP Client 4.2.5
Copyright 2004-2013 Internet Systems Consortium.
All rights reserved.
For info, please visit https://www.isc.org/software/dhcp/

Listening on LPF/enp0s8/08:00:27:33:36:16
Sending on   LPF/enp0s8/08:00:27:33:36:16
Listening on LPF/enp0s3/08:00:27:c9:d8:70
Sending on   LPF/enp0s3/08:00:27:c9:d8:70
Sending on   Socket/fallback
DHCPRELEASE on enp0s8 to 192.168.55.2 port 67 (xid=0x3253d99c)
DHCPRELEASE on enp0s3 to 10.5.2.11 port 67 (xid=0x61788c86)
```

* Redemander une IP :

```
[root@localhost akha]# dhclient -v
Internet Systems Consortium DHCP Client 4.2.5
Copyright 2004-2013 Internet Systems Consortium.
All rights reserved.
For info, please visit https://www.isc.org/software/dhcp/

Listening on LPF/enp0s8/08:00:27:33:36:16
Sending on   LPF/enp0s8/08:00:27:33:36:16
Listening on LPF/enp0s3/08:00:27:c9:d8:70
Sending on   LPF/enp0s3/08:00:27:c9:d8:70
Sending on   Socket/fallback
DHCPDISCOVER on enp0s8 to 255.255.255.255 port 67 interval 8 (xid=0x66afdecf)
DHCPDISCOVER on enp0s3 to 255.255.255.255 port 67 interval 6 (xid=0x7787c1bd)
DHCPREQUEST on enp0s8 to 255.255.255.255 port 67 (xid=0x66afdecf)
DHCPOFFER from 192.168.55.2
DHCPACK from 192.168.55.2 (xid=0x66afdecf)
bound to 192.168.55.4 -- renewal in 487 seconds.
```