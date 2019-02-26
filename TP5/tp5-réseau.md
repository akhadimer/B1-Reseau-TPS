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

* Après avoir effectué la commande ``sudo systemctl start dhcpd``, on peut remarquer que le serveur dhcpd est bien activé avec la commande ``systemctl status dhcpd`` :

![https://image.noelshack.com/fichiers/2019/09/2/1551213875-result-status-dhcpd.png]()