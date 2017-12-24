# __2. Instalation du sytème de base__

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
        pacstrap /mnt zip unzip p7zip vim mc alsa-utils syslog-ng mtools dosfstools fish lsb-release ntfs-3g exfat-utils wireless_tools dialog wpa_supplicant wpa_actiond ifpluged sudo git ntp cronie wget

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

* Il faut installer les paquets `grub` et `os-prober` pour le dualboot:

        $pacman -S grub os-prober
* Pour installer sur le disque `/dev/sda`:

        $grub-install --no-floppy --recheck /dev/sda
* Générer le fichier de configuration principal en utilisent l'outil `grub-config` pour générer `grub.cfg`

        $grub-mkconfig -o /boot/grub/grub.cfg

## __Démonter le tout__

* Sortez de l'environement __chroot__:

        $exit
    Puis,

        $umount -R /mnt
    Vous pouvez redémmarer l'ordinateur

        $reboot