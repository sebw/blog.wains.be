---
date: 2007-01-30
title: "PMB - php-yaz sous Redhat 4 / CentOS 4 / Fedora (FR)"
---







categories:
- Linux


Nous utilisons PMB au travail ([http://www.sigb.net](http://www.sigb.net)).
Ce logiciel offre une possibilité intéressante qui permet de contacter des bibliothéques PMB externes, cette fonction fait appel à un module appelé "YAZ" et son module au niveau du serveur web "PHP-YAZ".

Nous utilisons un serveur CentOS (équivalent Redhat Enterprise RHEL, voire Fedora Core).

Le problème se situait au niveau des RPM disponibles sur le site des développeurs de Yaz.
En installant les RPM yaz et libyaz, ça ne fonctionnait pas, le module PHP (php-yaz.so ou yaz.so) était introuvable.



Comment procéder ?

Il faut compiler le module php-yaz manuellement, or la compilation dépend de yaz et de ses librairies. En installant les RPM en version 2.1.8, ils sont détectés comme une version inférieure à la version 2.0.13, ce qui fait planter la compilation. Il faut donc compiler yaz manuellement également (j'ai fait ça sur une machine de test), il faut s'assurer d'avoir les paquets de développement habituels (j'ai dû compléter la machine de compilation du paquet "libgcrypt-devel")

**Sur la machine de compilation :**
1. Désinstaller les RPMS yaz si ils sont installés : yum erase yaz libyaz
2. Télécharger la dernière version de Yaz : http://ftp.indexdata.dk/pub/yaz/yaz-2.1.48.tar.gz
3. Le compiler en suivant la procédure habituelle :
3.1. gzip -d yaz-2.1.48.tar.gz
3.2. tar xvf yaz-2.1.48.tar
3.3. ./configure

Il doit ressortir ceci comme résultat :
`------------------------------------------------------------------------
Configuration:
  YAZ Package:                yaz
  YAZ Version:                2.1.48
  Bugreport:                  x
  Source code location:       .
  C Preprocessor:             gcc -E
  C Preprocessor flags:       
  C Compiler:                 gcc
  C Compiler flags:           -g -O2
  Linker flags:               
  Linked libs:                -lexslt -lxslt -lgcrypt -lgpg-error -lxml2 -lpthread -lz -lm    -L/usr/kerberos/lib -lssl -lcrypto -lgssapi_krb5 -lkrb5 -lcom_err -lk5crypto -lresolv -ldl -lz   -lpthread
  Host System Type:           i686-pc-linux-gnu
  Install path:               /usr/local
  Automake:                   ${SHELL} /var/tmp/yaz-2.1.48/config/missing --run automake-1.8
  Archiver:                   ar
  Ranlib:                     ranlib
------------------------------------------------------------------------`

3.4. make
3.5. make install
4. Télécharger la dernière version de php-yaz (qui porte également le nom yaz, vive la confusion) : http://pecl.php.net/get/yaz-1.0.8.tgz
4.1. gzip -d yaz-1.0.8.tgz
4.2. tar xvf yaz-1.0.8.tar
Comme indiqué sur le site [http://www.indexdata.dk/keystone/doc/install-php-yaz.tkl](http://www.indexdata.dk/keystone/doc/install-php-yaz.tkl) :
4.3. phpize (il doit donner quelques warnings qui ne sont apparemment pas importants)
4.4. ./configure --with-php-config=/usr/bin/php-config
4.5. make (ne pas faire make install, ce n'est pas nécessaire sur la machine de compilation)

Après compilation, le module php yaz.so se retrouve dans le sous-répertoire modules/ du répertoire de compilation.

**Sur le serveur de production :**
1. Il suffit alors de copier yaz.so vers le serveur de production PMB, dans le répertoire "/usr/lib/php4/" pour CentOS 4.4

Exemple : `scp root@compilation:/tmp/yaz/modules/yaz.so root@production:/usr/lib/php4/`

2. Ajouter l'extension yaz.so dans /etc/php.ini sous la section "Dynamic Extensions" :

`;;;;;;;;;;;;;;;;;;;;;;
; Dynamic Extensions ;
;;;;;;;;;;;;;;;;;;;;;;
extension=yaz.so`

3. Installer les RPM yaz et libyaz que l'on trouve ici : [http://ftp.indexdata.dk/pub/yaz/redhat/fc3/](http://ftp.indexdata.dk/pub/yaz/redhat/fc3/)
Il faut utiliser les RPM Fedora Core 3 pour RedHat/CentOS 4, ou alors recompiler depuis les sources RPM.

4. Rédemarrer le serveur Apache : service httpd condrestart

5. Faire une requête Z39.50 dans PMB, vérifier les logs et voir si il ne ressort pas un message tel que : 
`PHP Warning:  Unknown(): Unable to load dynamic library '/usr/lib/php/modules/yaz.so' - /usr/lib/php/modules/yaz.so: undefined symbol: ZOOM_resultset_sort in Unknown on line 0`

6. Optionnel : créer un fichier phpinfo.php dans votre arborescence web :

` `

Ensuite, http://hostname/phpinfo.php

Vous devez voir la configuration de votre server et une section Yaz :

`yaz
YAZ Support 	enabled
PHP/YAZ Version 	1.0.8
YAZ Version 	2.1.8
Compiled with YAZ version 	2.1.48`

Dans un souci de facilité, de propreté et de maintenance, j'utilise uniquement des RPM sur les machines de production et je compile les choses introuvables en RPM sur une machine test.
Vous pouvez bien entendu toujours tout compiler à la main sur votre serveur de production, mais je déconseille vivement celà (celà évite d'avoir des RPMS de développement et des fichiers sources éparpillés sur le système de production).
