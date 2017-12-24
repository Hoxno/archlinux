## Introduction

Le but de ce tutoriel est de m'aider à installer archlinux c'est un tutoriel que j'ai écrit à l'aide de différent tutoriel que j'ai trouvé sur internet. Ce tutoriel n'est pas vraiment personnel, je publie mon tutoriel ici car je me dis que ça purrait aider des personnes.

# 1. __Préparation avant l'installation__

* __Disposition du clavier__

    Pour changer l'agencement du __clavier__, utilisez la commande `loadkeys`.

        loadkeys fr-pc

* __Connexion eu réseau wifi__

    Sur un ordinateur portable si vous voulez vous connectez en wifi il suffit d'utilisez la commande `wifi-menu`.

        wifi-menu

## __Partitionnement du disque__

* Patitionnement du disque pour une installation en dual boot avec windows 10.

        $cfdisk

    | Nom de la Partition | Taille | Format | Type |
    | :----------- | :------: | :-------: | :------|
    | /dev/sda1 | 500M | ntfs | Boot MS |
    | /dev/sda2 | 600G | ntfs | Partition Windows 10 |
    | /dev/sda3 | 512M | ext2 | Grub |
    | /dev/sda4 | 400G | ext4 | Partition Linux |

* Ensuite on va formater nos partition qu'on a créé précedement

        mkfs.ext2 /dev/sda3
        mkfs.ext4 /dev/sda4

* Montage des partitions

        $mount /dev/sda4 /mnt
        $mkdir /mnt/boot
        $mount /dev/sda3 /mnt/boot
