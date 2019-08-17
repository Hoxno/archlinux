Sommaire
========

<!---
BEGIN
-->

[1 - Préparation](#1.préparation)

[2 - Installation de la base](#2.installation-du-systeme-de-base)

[3 - Installation des differents paquets]()

[4 - Configuration]()

## Introduction

Le but de ce tutoriel est de m'aider à installer archlinux sur mon environement de travailler et peut par la suite de créer un script d'installation. Ce tutorriel est basé sur le travaille de [Fréderic Bezies](https://github.com/FredBezies/arch-tuto-installation).

# 1. Préparation

* __Disposition du clavier__

    Pour changer l'agencement du __clavier__, utilisez la commande `loadkeys`.

        loadkeys fr-pc

* __Connexion eu réseau wifi__

    Sur un ordinateur portable si vous voulez vous connectez en wifi il suffit d'utilisez la commande `wifi-menu`.

        wifi-menu

## __Partitionnement du disque__
Pour un dique dur ou un ssd
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

Pour le d'un ssd nvme

# 2. Instalation du sytème de base

## __Selection du miroir__

Avant l'installation, il peut être intéressant de modifier `/etc/pacman.d/mirrorlist` pour bénéficier d'un mirroir plus proche de chez vous et plus rapide.
Pour la selection des mirrors il y a différente méthode ma préferer est la deuxième.

## __Première méthode__

_Le package ```pacman``` met à disposition un script bash, `/usr/bin/rankmirrors`, dans lequel peut être utilisé afin de classer les mirroirs diponibles en terme de rapidité._

* Commençons par créer un fichier de backup de /usr/bin/rankmirrors :

        cp /etc/pacman.d/mirrorlist /etc/pacman.d/mirrorlist.backup

* Editons maintenant le fichier backup. Nous allons décommenter TOUS les miroirs afin que rankmirrors puisse les tester. Pour se faire, sed s'avère très utile.

        sed -s 's/^#Server/Server/' /etc/pacman.d/mirrorlist.backup

* Pour finir, nous allons laisser ```rankmirrors``` trouve les 10 meilleurs mirroirs, et écrire le résultat directement dans /etc/pacman.d/mirrorlist

        rankmirrors -n 10 /etc/pacman.d/mirrorlist.backup > /etc/pacman.d/mirrorlist

## __Deuxième méthode__

* On édite le ficher `/etc/pacman.d/mirrorlist` :

        nano /etc/pacman.d/mirrorlist

* ensuite avec une combinaison de touche `ALT + R`. On mettra dans un premier temps `Server` (sans les guillemets). Et ensuite `#Server`.

## __Installation des paquets de base__

* Il suffit d'utiliser le script `pacstrap` en lui indiquant le dossier correspondant à la racine du système suivi des paquets ou groupes à installer (séparés par un espace). Pour le système de base :

        pacstrap /mnt base base-devel 
        pacstrap /mnt zip unzip p7zip vim mc alsa-utils syslog-ng mtools dosfstools fish lsb-release ntfs-3g exfat-utils wireless_tools dialog wpa_supplicant wpa_actiond ifplugd sudo git ntp cronie wget

### __Configuration du sytème__

Pour une configuration de base:

* Générer le fichier `/etc/fstab`

        $genfstab -U -p /mnt >> /mnt/etc/fstab

* Chrooter dans le nouveau système:

        $arch-chroot /mnt

* Renseignez le _nom de la machine_ dans le fichier `/etc/hostname`:

        $echo NomDeLaMachine > /etc/hostname

* Editer le fichier `/etc/vconsole.conf` afin d'y spécifier la dispotion du clavier que vous souhaitez utiliser:

        $echo KEYMAP=fr > /etc/vconsole.conf

* Ajoutez le nom de la locale au fichier `etc/locale.conf`:

        $echo LANG="fr_FR.UTF-8" > /etc/locale.conf

* Editer le fichier `/etc/locale.gen`

        $nano /etc/locale.gen

* et décommenter votre locale puis executez la commande suivante

        $locale-gen

* Vous pouvez spécifier la locale pour la session courante:

        $export LANG=fr_FR.UTF-8

* Créer un lien symbolique `/etc/localtime` afin de choisir votre _fuseau horaire_, par exemple pour la France:

        $ln -sf /usr/share/zoneinfo/Europe/Paris /etc/localtime

* Configurer `/etc/mkinitcpio.conf` et créez les RAMdisks initiaux avec:

        $mkinitcpio -p linux

* Définissez un mot de passe pour le __root__:

        $passwd root

## __Installation d'un bootloader__

##__(Version bios)__

* Il faut installer les paquets `grub` et `os-prober` pour le dualboot:

        $pacman -S grub os-prober
* Pour installer sur le disque `/dev/sda`:

        $grub-install --no-floppy --recheck /dev/sda
* Générer le fichier de configuration principal en utilisent l'outil `grub-config` pour générer `grub.cfg`

        $grub-mkconfig -o /boot/grub/grub.cfg

##__(Version EFI)__

## __Démonter le tout__

* Sortez de l'environement __chroot__:

        $exit
    Puis,

        $umount -R /mnt
    Vous pouvez redémmarer l'ordinateur

        $reboot

# __3. Configuration du système__

Une fois que le système redémarre, on se conecte en __root__.

* On se connecte d'abord à internet avec la commande

        wifi-menu
* Si on veut que le système se connecter automatiquement à internet au démmarage, il suffit d'utiliser la commande

        netctl enable 'profile'
* Pour connaître le profile, j'utilise la commande

        netctl list

* Création d'un compte utilisateur

    Avant d'installer nos logiciels préferer, nous allons d'abord créé un compte utilisateur avec la commande suivante:

        useradd -m -g wheel -c 'Nom de l'utilisateur' -s /usr/bin/fish nom-de-l'utilisateur
        passwd nom-de-l'utilisateur

    Avant de finir, on va configurer sudo en utilisant visudo. En effet, il nous suffit de modifier une ligne pour que l'on puisse accèder en tant qu'utilisateur classique aux droits complets sur la machine.

    Il faut aller enlever le # sur la ligne qui suit:

    ##Uncomment to allow members of group wheel to execute any command

    Et ensuite `Echap: w + q` pour conserer la modification.

* Si on veut avoir les logs en clair en cas de problème, il faut modifier avec nano (ou vim) le fichier __/etc/systemd/journald.conf__ en remplacant la ligne:

        #ForwardToSyslog=no

    par:

        ForwardToSyslog=yes

## Installation de ```trizen```

* Nous allons installer ```trizen```, car si on veut installer des paquets qui ne sont pas disponible sur les dépots officiel, alors va devoir installer depuis des dépot communautaire.

        git clone git clone https://aur.archlinux.org/trizen.git
        cd trizen-git
        makepkg -si

## Installation des plugins multimédia

* On va installer l'ensemble des greffons gstreamer qui nous donneront accès aux fichiers multimédias.

        pacman -S gst-plugins-{base,good,bad,ugly} gst-libav

* Installation de Xorg

        pacman -S xorg-{server,xinit,apps} xf86-input-{mouse,keyboard,libinput} xdg-user-dirs

## Installation des drivers

* On commence par cofigurer les périphérique audio, on lance ```alsamixer```, pour configurer le niveau sonore.

        alsamixer

* En suite on écrit:

        alsactl-store
    Pour que la configuration d'alsamixer soit conserver.

### Imprimantes

* Dans mon cas c'est une imprimante hp, donc:

        pacman -S cups cups-pdf hplip python-pyqt5

### Installation des pilotes graphiques

* Comme c'est une installation pour un pc portable qui comporte la technologie optimus alors, on a installer bumblebee

        pacman -S intel-dri xf86-video-intel bumblebee bbswitch primus lib32-primus nvidia nvidia-utils lib32-nvidia-utils lib32-intel-dri opencl-nvidia lib32-virtualgl virtualgl lib32-primus

### Installation du pilote pour la souris

* Pour une souris corsair, je vais installer le paquet ckb

        trizen -S qt5-base zlib ckb-next-git
* Ensuite

        sudo systemctl start ckb-daemon.service
        sudo systemctl enable ckb-daemon.service

### Installation des polices

* C'est une ensemble de police à installer

        pacman -S ttf-{bitstream-vera,liberation,freefont,dejavu,roboto,cheapskate,arphic-uming,baekmuk,font-awesome}
        pacman -S xorg-fonts-type1 sdl_ttf gsfonts artwiz-fonts
        pacman -S font-{bh-ttf,bitstream-speedo}

## Installation d'un environement de bureau

* Pour ce tutoriel on va installer mate-desktop

        trizen -S mate mate-extra gnome-icon-theme python2-pyinotify accountsservice thunderbird-i18-fr atril ffmpegthumbnailer xscreensaver pavucontrol pulseaudio pulseaudio-alsa libcanberra-{pulse,gstreamer} gtk3-print-backends system-config-printer mate-tweak brisk-menu

* Ensuite on installer notre écran de connexion

        pacman -S lightdm lightdm-gtk-greeter
        pacaur -S lightdm-gtk-greeter-settings

* Pour avoir le bon agencement clavier dès la saisie du premier caractère du mot de passe, il faut entrer la commande suivante avant de lancer pour la première fois lightdm:

        sudo localectl set-x11-keymap fr
* Ensuite on peut lancer l'écran de connexion lightdm et mate avec la commande:

        sudo systemctl start accounts-daemon
        sudo systemctl start lightdm

* Et si tout se passe bien, on peut utiliser

        sudo systemctl enable accounts-daemon
        sudo systemctl enable lightdm

## Installation des logiciels

* Logiciel graphique

        trizen -S gimp gimp-fr inkscape

* Bureautique

        trizen -S libreoffice-fresh-fr

* Internet

        trizen firefox-i18n-fr chrominium vivaldi-snapshot jdownloader

* Multimédia

        trizen -S vlc mpv spotify molotov

* Editeur de texte:

        trizen -S atom-editor-bin visual-studio-code-bin

* Terminal

        trizen -S deepin-terminal

* Outils système

        trizen -S spacer