Sommaire
========

<!---
BEGIN
-->

0. [Introduction](#introduction)
1. [Préparation](#1-préparation)
   1. [Partitionnement du disque ](#partitionnement-du-disque)
        1. [Cas d'un dual boot](#cas-dun-dual-boot)
           1. [Pour un disque dur ou un ssd](#)
           1. [Pour un disque ssd NVME](#)
        1. [Cas d'un boot seul](#cas-dun-boot-seul)
           1. [Pour un disque dur ou un ssd](#)
           1. [Pour un disque ssd NVME](#)
1. [Installation de la base](#2-instalation-du-sytème-de-base)
   1. [Selection du miroir](#selection-du-miroir)
        1. [Première méthode (plus simple)](#première-méthode-plus-simple)
        1. [Deuxième méthode](#deuxième-méthode)
        1. [Installation des paquets de base](#installation-des-paquets-de-base)
        1. [Configuration du sytème](#configuration-du-sytème)
        1. [Installation d'un bootloader](#installation-dun-bootloader)
           1. [En version Bios](#version-bios)
           1. [En version UEFI](#version-uefi)
        1. [Redémmarage](#redémmarage)
1. [Configurations du systèmes](#3-configuration-du-système)
    1. [Installation de trizen](#installation-de-trizen)
    1. [Installation des plugins multimédia](#installation-des-plugins-multimédia)
    1. [Installation des drivers](#nstallation-des-drivers)<br/>
       1. [Imprimantes](#imprimantes)
       1. [Installation des pilotes graphiques](#installation-des-pilotes-graphiques)
       1. [Installation des polices](#installation-des-polices)
    1. [Installation d'un environement de bureau](#installation-dun-environement-de-bureau)
    1. [Installation des logiciels](#installation-des-logiciels)
1. [Configuration](#4-configuration)
    1. [Configuration de fish](#configuration-de-fish)
    1. [Configuration d'un environement de dev](#configuration-dun-environement-de-dev)
        1. [Installation de PHP](#installation-de-php)
        1. [Installation de mariadb](#installation-de-mariadb)
    1. [En cours de finalisation](#)
    1. [En cours de finalisation](#)
    1. [En cours de finalisation](#)

# Introduction

Le but de ce tutoriel est de m'aider à installer archlinux sur mon environement de travailler et peut par la suite de créer un script d'installation. Ce tutorriel est basé sur le travaille de [Fréderic Bezies](https://github.com/FredBezies/arch-tuto-installation).

# 1. Préparation

* __Disposition du clavier__

    Pour changer l'agencement du __clavier__, utilisez la commande `loadkeys`.

        $loadkeys fr-pc

* __Connexion eu réseau wifi__

    Sur un ordinateur portable si vous voulez vous connectez en wifi il suffit d'utilisez la commande `wifi-menu`.

        $wifi-menu

## __Partitionnement du disque__

Le partitionnement peut être fait avant de démarrer sur le live (avec gparted, par exemple), mais il peut aussi être fait à ce moment à l'aide de l'un des différents utilitaires disponibles : fdisk, parted, cfdisk, etc.

## Cas d'un dual boot

__Pour un dique dur ou un ssd__

* Patitionnement du disque pour une installation en dual boot avec windows 10.

| Nom de la Partition | Taille | Format | Type |
| :----------- | :------: | :-------: | :------|
| /dev/sda1 | 500M | ntfs | Boot MS |
| /dev/sda2 | 600G | ntfs | Partition Windows 10 |
| /dev/sda3 | 512M | ext2 | Grub |
| /dev/sda4 | 400G | ext4 | Partition Linux |

* Pour une machine en demarrage en bios, lancer la commande:

        $cfdisk

* Pour une machine en demarrage UEFI, lancer la commande ```cgdisk (nom du volume)```:

        $cgdisk /dev/sda1

* Ensuite on va formater nos partition qu'on a créé précedement

        mkfs.ext2 /dev/sda3
        mkfs.ext4 /dev/sda4

* Montage des partitions

        $mount /dev/sda4 /mnt
        $mkdir /mnt/boot
        $mount /dev/sda3 /mnt/boot

### Pour le d'un ssd nvme

* Patitionnement du disque pour une installation en dual boot avec windows 10.

| Nom de la Partition | Taille | Format | Type |
| :----------- | :------: | :-------: | :------|
| /dev/nvme0n1p1 | 500M | ntfs | Boot MS |
| /dev/nvme0n1p2 | 600G | ntfs | Partition Windows 10 |
| /dev/nvme0n1p4 | 512M | ext2 | Grub |
| /dev/nvme0n1p4 | 400G | ext4 | Partition Linux |

* Pour une machine en demarrage en bios, lancer la commande:

        $cfdisk

* Pour une machine en demarrage UEFI, lancer la commande ```cgdisk (nom du volume)```:

        $cgdisk /dev/nvme0n1

* Ensuite on va formater nos partition qu'on a créé précedement

        mkfs.ext2 /dev/nvme0n1p3
        mkfs.ext4 /dev/nvme0n1p4

* Montage des partitions

        $mount /dev/nvme0n1p4 /mnt
        $mkdir /mnt/boot
        $mount /dev/sdanvme0n1p3 /mnt/boot

## Cas d'un boot seul

### Pour un dique dur ou un ssd

| Nom de la Partition | Taille | Format | Type |
| :----------- | :------: | :-------: | :------|
| /dev/sda1 | 300M | vfat | /boot/efi |
| /dev/sda2 | 8G | swapon | swap |
| /dev/sda3 | 30G | ext4 | / |
| /dev/sda4 | le reste | ext4 | /home |

* Pour une machine en demarrage en bios, lancer la commande:

        $cfdisk

* Pour une machine en demarrage UEFI, lancer la commande ```cgdisk (nom du volume)```:

        $cgdisk /dev/sda1

### Pour une table de partition MBR

* Ensuite on va formater nos partition qu'on a créé précedement

        $mkfs.ext2 /dev/sda1
        $mkfs.ext4 /dev/sda3
        $mkfs.ext4 /dev/sda4

* La partition swap est crée en utilisan ```mkswap```

        $mkswap /dev/sda2

### Pour une table de partition GPT

Pour une table GPT, le formatage est le même que pour une table MBR.

        $mkfs.vfat -F32 /dev/sda1

### Montage des partitions

Il faut monter les partitions précedement créées sous le dossier /mnt afin d'y installer le système. On utilise la commande ```mount```:

        $mount /dev/sda3 /mnt
        # Pour créer le dossier utilisateur, il nous faut monter la partition /home
        $mkdir /mnt/home && mount /dev/sda4 /mnt/home

La partition swap doit être également activé pour être détecté lors de la création du fstab:

        $swapon /dev/sda2

### Pour le d'un ssd nvme

| Nom de la Partition | Taille | Format | Type |
| :----------- | :------: | :-------: | :------|
| /dev/nvme0n1p1 | 300M | vfat | /boot/efi |
| /dev/nvme0n1p2 | 8G | swapon | swap |
| /dev/nvme0n1p3 | 30G | ext4 | / |
| /dev/nvme0n1p4 | le reste | ext4 | /home |

* Pour une machine en demarrage en bios, lancer la commande:

        $cfdisk

* Pour une machine en demarrage UEFI, lancer la commande:

        $cgdisk (nom du volume)

### Pour une table de partition MBR

* Ensuite on va formater nos partition qu'on a créé précedement

        $mkfs.ext2 /dev/nvme0n1p1
        $mkfs.ext4 /dev/nvme0n1p3
        $mkfs.ext4 /dev/nvme0n1p4

* La partition swap est crée en utilisan ```mkswap```

        $mkswap /dev/nvme0n1p2

### Pour une table de partition GPT

Pour une table GPT, le formatage est le même que pour une table MBR.

        $mkfs.vfat -F32 /dev/nvme0n1p1

### Montage des partitions

* Pour un ssd ou disque dur

Il faut monter les partitions précedement créées sous le dossier /mnt afin d'y installer le système. On utilise la commande ```mount```:

        $mount /dev/sda3 /mnt
        # Pour créer le dossier utilisateur, il nous faut monter la partition /home
        $mkdir /mnt/home && mount /dev/sda4 /mnt/home

La partition swap doit être également activé pour être détecté lors de la création du fstab:

        $swapon /dev/sda2

* Pour un disque SSD NVME

l faut monter les partitions précedement créées sous le dossier /mnt afin d'y installer le système. On utilise la commande ```mount```:

        $mount /dev/nvme0n1p3 /mnt
        # Pour créer le dossier utilisateur, il nous faut monter la partition /home
        $mkdir /mnt/home && mount /dev/nvme0n1p4 /mnt/home

La partition swap doit être également activé pour être détecté lors de la création du fstab:

        $swapon /dev/nvme0n1p2


# 2. Instalation du sytème de base

## __Selection du miroir__

Avant l'installation, il peut être intéressant de modifier `/etc/pacman.d/mirrorlist` pour bénéficier d'un mirroir plus proche de chez vous et plus rapide.
Pour la selection des mirrors il y a différente méthode ma préferer est la deuxième.

## __Première méthode__ (plus simple)

* On édite le ficher `/etc/pacman.d/mirrorlist` :

        $nano /etc/pacman.d/mirrorlist

* ensuite avec une combinaison de touche `ALT + R`. On mettra dans un premier temps `Server` (sans les guillemets). Et ensuite `#Server`.

## __Deuxième méthode__

_Le package ```pacman``` met à disposition un script bash, `/usr/bin/rankmirrors`, dans lequel peut être utilisé afin de classer les mirroirs diponibles en terme de rapidité._

* Commençons par créer un fichier de backup de /usr/bin/rankmirrors :

        $cp /etc/pacman.d/mirrorlist /etc/pacman.d/mirrorlist.backup

* Editons maintenant le fichier backup. Nous allons décommenter TOUS les miroirs afin que rankmirrors puisse les tester. Pour se faire, sed s'avère très utile.

        $sed -s 's/^#Server/Server/' /etc/pacman.d/mirrorlist.backup

* Pour finir, nous allons laisser ```rankmirrors``` trouve les 10 meilleurs mirroirs, et écrire le résultat directement dans /etc/pacman.d/mirrorlist

        $rankmirrors -n 10 /etc/pacman.d/mirrorlist.backup > /etc/pacman.d/mirrorlist

## __Installation des paquets de base__

* Il suffit d'utiliser le script `pacstrap` en lui indiquant le dossier correspondant à la racine du système suivi des paquets ou groupes à installer (séparés par un espace). Pour le système de base :

        $pacstrap /mnt base base-devel 
        $pacstrap /mnt zip unzip p7zip vim mc alsa-utils syslog-ng mtools dosfstools fish lsb-release ntfs-3g exfat-utils wireless_tools dialog wpa_supplicant wpa_actiond ifplugd sudo git ntp cronie wget curl

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

* Définissez un mot de passe __root__:

        $passwd root

## __Installation d'un bootloader__

### (Version bios)

* Il faut installer les paquets `grub` et `os-prober` pour le dualboot:

        $pacman -S grub os-prober
* Pour installer sur le disque `/dev/sda`:

        $grub-install --no-floppy --recheck /dev/sda
* Générer le fichier de configuration principal en utilisent l'outil `grub-config` pour générer `grub.cfg`

        $grub-mkconfig -o /boot/grub/grub.cfg

###(Version UEFI)

## __Redémmarage__

* Sortez de l'environement __chroot__:

        $exit
    Puis,

        $umount -R /mnt
    Vous pouvez redémmarer l'ordinateur

        $reboot

# 3. Configuration du système

Une fois que le système redémarre, on se conecte en __root__.

* On se connecte d'abord à internet avec la commande

        $wifi-menu

* Création d'un compte utilisateur<br />

    Avant d'installer nos logiciels préferer, nous allons d'abord créé un compte utilisateur avec la commande suivante:

        $useradd -m -g wheel -c 'Nom de l'utilisateur' -s /usr/bin/fish nom-de-l'utilisateur
        $passwd nom-de-l'utilisateur

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

        $git clone git clone https://aur.archlinux.org/trizen.git
        $cd trizen-git
        $makepkg -si

Et maintenant pour le reste du tutoriel, je vais utiliser trizen au lieu de paman pour installer les logicielles et drivers.

## Installation des plugins multimédia

* On va installer l'ensemble des greffons gstreamer qui nous donneront accès aux fichiers multimédias.

        $trizen -S gst-plugins-{base,good,bad,ugly} gst-libav

* Installation de Xorg

        $trizen -S xorg-{server,xinit,apps} xf86-input-{mouse,keyboard,libinput} xdg-user-dirs

## Installation des drivers

* On commence par cofigurer les périphérique audio, on lance ```alsamixer```, pour configurer le niveau sonore.

        $alsamixer

* En suite on écrit:

        $alsactl store
    Pour que la configuration d'alsamixer soit conserver.

### Imprimantes

* Dans mon cas c'est une imprimante hp, donc:

        $trizen -S cups cups-pdf hplip python-pyqt5

### Installation des pilotes graphiques

* Installation des pilote intel

        $trizen -S intel-dri xf86-video-intel lib32-intel-dri

### Installation des polices

* C'est une ensemble de polices à installer

        $trizen -S ttf-{bitstream-vera,liberation,freefont,dejavu,roboto,cheapskate,arphic-uming,baekmuk,font-awesome}
        $trizen -S xorg-fonts-type1 sdl_ttf gsfonts artwiz-fonts
        $trizen -S font-{bh-ttf,bitstream-speedo}

## Installation d'un environement de bureau

* Pour ce tutoriel on va installer mate-desktop

        $trizen -S mate mate-extra gnome-icon-theme python2-pyinotify accountsservice thunderbird-i18-fr atril ffmpegthumbnailer xscreensaver pavucontrol pulseaudio pulseaudio-alsa libcanberra-{pulse,gstreamer} gtk3-print-backends system-config-printer mate-tweak brisk-menu

* Ensuite on installer notre écran de connexion

        $trizen -S lightdm lightdm-gtk-greeter lightdm-gtk-greeter-settings

* Pour avoir le bon agencement clavier dès la saisie du premier caractère du mot de passe, il faut entrer la commande suivante avant de lancer pour la première fois lightdm:

        $sudo localectl set-x11-keymap fr
* Ensuite on peut lancer l'écran de connexion lightdm et mate avec la commande:

        $sudo systemctl start accounts-daemon
        $sudo systemctl start lightdm

* Et si tout se passe bien, on peut utiliser

        $sudo systemctl enable accounts-daemon
        $sudo systemctl enable lightdm

## Installation des logiciels

* Logiciel graphique

        $trizen -S gimp gimp-fr inkscape

* Bureautique

        $trizen -S libreoffice-fresh-fr

* Internet

        $trizen firefox-i18n-fr chrominium vivaldi-snapshot jdownloader

* Multimédia

        $trizen -S vlc mpv spotify molotov

* Editeur de texte:

        $trizen -S atom-editor-bin visual-studio-code-bin

* Terminal

        $trizen -S deepin-terminal

* Outils système

        $trizen -S spacer

# 4. Configuration

## Configuration de fish et vim

### Configuration de fish

1. Installation de oh-my-fish

Premièrement on télécharger `oh-my-fish`.

    $curl -L https://get.oh-my.fish > install
    $fish install --path=~/.local/share/omf --config=~/.config/omf

2. Installation d'un thème bullet-train

On commence par télécharger le thème

        omf install https://github.com/kobanyan/bullet-train-fish-theme

Ensuite dans le fichier ```.config/fish/config.fish```

        set -g BULLETTRAIN_PROMPT_ORDER \
        git \
        context \
        dir \
        time

### Configuration de vim

1. Preimièrement nous allons commencer par éditer le fichier avec les lignes suivantes:

        set nocompatible
        filetype plugin on
        set smartindent
        set tabstop=4
        set shiftwidth=4
        set expandtab
        " Set VIM history
        set history=588

        " Enable line number
        set number

        " Enable highlight syntax
        syntax enable

        " Set encoding
        set encoding=utf8

        " Show the current command
        set showcmd

        set rtp+=/usr/lib/python3.7/site-packages/powerline/bindings/vim/
        set laststatus=2
        set t_Co=256

        inoremap {<cr> {<cr>}<c-o><s-o>
        inoremap [<cr> [<cr>]<c-o><s-o>
        inoremap (<cr> (<cr>)<c-o><s-o>

2. Ensuite on va configurer vim avec Vundle

 git clone https://github.com/VundleVim/Vundle.vim.git ~/.vim/bundle/Vundle.vim

Et maintenant dans le fichier `.vimrc` ecrire:

        set nocompatible              " be iMproved, required
        filetype off                  " required

        " set the runtime path to include Vundle and initialize
        set rtp+=~/.vim/bundle/Vundle.vim
        call vundle#begin()
        " alternatively, pass a path where Vundle should install plugins
        "call vundle#begin('~/some/path/here')

        " let Vundle manage Vundle, required
        Plugin 'VundleVim/Vundle.vim'

        " The following are examples of different formats supported.
        " Keep Plugin commands between vundle#begin/end.
        " plugin on GitHub repo
        Plugin 'tpope/vim-fugitive'
        " plugin from http://vim-scripts.org/vim/scripts.html
        " Plugin 'L9'
        " Git plugin not hosted on GitHub
        Plugin 'git://git.wincent.com/command-t.git'
        " git repos on your local machine (i.e. when working on your own plugin)
        Plugin 'file:///home/gmarik/path/to/plugin'
        " The sparkup vim script is in a subdirectory of this repo called vim.
        " Pass the path to set the runtimepath properly.
        Plugin 'rstacruz/sparkup', {'rtp': 'vim/'}
        " Install L9 and avoid a Naming conflict if you've already installed a
        " different version somewhere else.
        " Plugin 'ascenator/L9', {'name': 'newL9'}

        " All of your Plugins must be added before the following line
        call vundle#end()            " required
        filetype plugin indent on    " required
        " To ignore plugin indent changes, instead use:
        "filetype plugin on
        "
        " Brief help
        " :PluginList       - lists configured plugins
        " :PluginInstall    - installs plugins; append `!` to update or just :PluginUpdate
        " :PluginSearch foo - searches for foo; append `!` to refresh local cache
        " :PluginClean      - confirms removal of unused plugins; append `!` to auto-approve removal
        "
        " see :h vundle for more details or wiki for FAQ
        " Put your non-Plugin stuff after this line

3. Et pour terminer ont va installer les plugins, avec la commande

        :PluginInstall
Dans vim.

## Configuration d'un environement de dev

### Installation de php

On commence par installer composer, php et quelque extension de php.

    $trizen -S composer php php-{gd,intl,sqlite} phpdox

Pour la configuration de php on va décommenter les ligne suivant dans le fichier /etc/php/php.ini

    extension=curl.so
    extension=gd.so
    extension=iconv.so
    extension=intl.so
    extension=pdo_sqlite.so
    extension=sqlite3.so

Et ont va aussi modifier dans ce même fichier, pour avoir les erreurs lors du dévelloppement

    display_errors=On

Une dernière étape pour la configuration de php c'est d'installer xdebug

    $trizen -S xdebug

Pour activer xdebug il faut décommenter tous les lignes dans le fichier `/etc/php/conf.d/xdebug.ini`

    zend_extension=xdebug.so
    xdebug.remote_enable=on
    xdebug.remote_host=127.0.0.1
    xdebug.remote_port=9000
    xdebug.remote_handler=dbgp

### Installation de mariadb

On commence par installer mariadb

Sur archlinux il est conseiller d'installer `mariadb`.

    $trizen -S mariadb

Ensuite demarra le service mariadb

    $sudo systemctl start maridb

Il y a une étape importante à faire qui est de sécuriser mariadb

    $sudo mysql_secure_installation

Pour que mariadb puisse communiquer avec php, il faut aller dans le fichier /etc/php/php.ini et décommenter les lignes

    extension=pdo_mysql.so
    extension=mysqli.so