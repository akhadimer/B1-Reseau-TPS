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
        (config-if)# ip address 10.5.3.1 255.255.255.254
        (config-if)# no shut
        ```

    * Routeur 2 :
            Pour définir l'adresse IP sur la carte ethernet du routeur2 qui est connecté au routeur1 :

            ```
            # conf t
            (config)# interface ethernet 0/0
            (config-if)# ip address 10.5.3.2 255.255.255.254
            (config-if)# no shut
            ```

            Pour définir l'adresse IP sur la carte ethernet du routeur2 qui est connecté au switch :

            # conf t
            (config)# interface ethernet 0/1
            (config-if)# ip address 10.5.2.254 255.255.255.0
            (config-if)# no shut
            ```