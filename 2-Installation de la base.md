2. __Instalation du sytème de base__
-------------------------------------


* __Selection du miroir__

__Première méthode__

Commençons par créer un fichier backup


    $cp /etc/pacman.d/mirrorlist /etc/pacman.d/mirrorlist.backup

Et on commente tous les lignes commencent par Server:

    $sed -s 's/^Server/#Server' /etc/pacman.d/mirrorlist.backup > /etc/pacman.d/mirrorlist

__Deuxième méthode__

On édite le ficher `/etc/pacman.d/mirrorlist` :

    $nano /etc/pacman.d/mirrorlist

ensuite avec une combinaison de touche ` ALT + R`. On netre dans un premier temps `Server` (sans les guillemets). Et ensuite `#Server`.

* __Installation des paquets de base__


    $pacstrap /mnt base base-devel
    $pacstrap /mnt zip unzip p7zip vim mc alsa-utils syslog-ng mtools dosfstools
    $lsb-release ntfs-3g exfat-utils

* __Configuration du sytème__

Générer le fichier `/etc/fstab`

    $genfstab -U -p /mnt >> /mnt/etc/fstab

Chrooter dans le nouveau système:

    $arch-chroot /mnt

Renseignez le _nom de la machine_ dans le fichier `/etc/hostname`:

    $echo NomDeLaMachine > /etc/hostname

Editer le fichier `/etc/vconsole.conf` afin d'y spécifier la dispotion du clavier que vous souhaitez utiliser:

    $echo KEYMAP=fr > /etc/vconsole.conf

Ajoutez le nom de la locale au fichier `etc/locale.conf`:

    $echo LANG="fr_FR.UTF-8" > /etc/locale.conf
Editer le fichier `/etc/locale.gen`

    $nano /etc/locale.gen
et décommenter votre locale puis executez la commande suivante

    $locale-gen
Vous pouvez spécifier la locale pour la session courante:

    $export LANG=fr_FR.UTF-8
Créer un lien symbolique `/etc/localtime` afin de choisir votre _fuseau horaire_, par exemple pour la France:

    $ln -sf /usr/share/zoneinfo/Europe/Paris /etc/localtime
Configurer `/etc/mkinitcpio.conf` et créez les RAMdisks initiaux avec:

    $mkinitcpio -p linux

Définissez un mot de passe pour le __root__:

    $passwd root
* __Installation d'un bootloader__

Il faut installer les paquets `grub` et `os-prober` pour le dualboot:

    $pacman -S grub os-prober
Pour installer sur le disque `/dev/sda`:

    $grub-install --no-floppy --recheck /dev/sda
Générer le fichier de configuration principal en utilisent l'outil `grub-config` pour générer `grub.cfg`

    $grub-mkconfig -o /boot/grub/grub.cfg

* Installation de NetworkManager


    $pacman -Syy networkmanager
    $systemctl enable NetworkManager
    
Installer le paquet `dialog` et `wpa_supplicant`

    $pacman -S dialog wpa_supplicant

* __Démonter le tout__

Sortez de l'environement __chroot__:

    $exit
Puis,

    $umount -R /mnt
Vous pouvez redémmarer l'ordinateur

    $reboot