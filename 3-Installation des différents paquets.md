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
