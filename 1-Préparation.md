1. __Préparation avant l'installation__
-----------------------------------

 * __Disposition du clavier__


Pour changer l'agencement du __clavier__, utilisez la commande `loadkeys`.

    $loadkeys fr-pc
* __Connexion eu réseau wifi__

Sur un ordinateur portable si vous voulez vous connectez en wifi il suffit d'utilisez la commande `wifi-menu`.

    $wifi-menu
    

* Mise à jour de l'heure du sytème
    
    `$timedatectl`
    

* Si l'heure n'est pas reglé alors:

    `$timedatectl set-ntp true`
 
* __Partitionnement du disque__

Patitionnement du disque pour une installation en dual boot avec windows 10.

    $cfdisk
    

| Nom de la Partition | Taille | Format | Type |
| :----------- | :------: | :-------: | :------|
| /dev/sda1 | 500M | ntfs | Boot MS |
| /dev/sda2 | 600G | ntfs | Partition Windows 10 |
| /dev/sda3 | 512M | ext2 | Grub |
| /dev/sda4 | 400G | ext4 | Partion Linux |
* Formater les partitions


    $mkfs.ext2 /dev/sda3
    $mkfs.ext4 /dev/sda4

* Montage des partitions


    $mount /dev/sda4 /mnt
    $mkdir /mnt/boot
    $mount /dev/sda3 /mnt/boot

****