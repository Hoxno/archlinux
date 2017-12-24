## Configuration zsh

1. Installation de oh-my-zsh

Premièrement on télécharger `oh-my-zsh`.

    sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"

2. Installation d'un thème bullet-train

On commence par télécharger le thème

Ensuite pour activer le thème on va dans le fichier `.zshrc`, à ligne ``.




## Configuration d'un environement de dev

1. Installation de php

On commence par installer composer, php et quelque extension de php.

    pacaur -S composer php php-{gd,intl,mcrypt,sqlite} phpdox

Pour la configuration de php on va décommenter les ligne suivant dans le fichier /etc/php/php.ini

    extension=curl.so
    extension=gd.so
    extension=iconv.so
    extension=intl.so
    extension=mcrypt.so
    extension=pdo_sqlite.so
    extension=sqlite3.so

Et ont va aussi modifier dans ce même fichier, pour avoir les erreurs lors du dévelloppement

    display_errors=On
Une dernière étape pour la configuration de php c'est d'installer xdebug

    pacaur -S xdebug
Pour activer xdebug il faut décommenter tous les lignes dans le fichier `/etc/php/conf.d/xdebug.ini`

    zend_extension=xdebug.so
    xdebug.remote_enable=on
    xdebug.remote_host=127.0.0.1
    xdebug.remote_port=9000
    xdebug.remote_handler=dbgp

2. Installation de mariadb

On commence par installer mariadb

Sur archlinux il est conseiller d'installer `mariadb`.

    pacaur -S mariadb

Ensuite demarra le service mariadb

    sudo systemctl start maridb

Il y a une étape importante à faire qui est de sécuriser mariadb

    mysql_secure_installation

Pour que mariadb puisse communiquer avec php, il faut aller dans le fichier /etc/php/php.ini et décommenter les lignes

    extension=pdo_mysql.so
    extension=mysqli.so