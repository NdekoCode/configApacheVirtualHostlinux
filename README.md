# configuration d'un VirtualHost avec linux sous un serveur Apache

Apres avoir installer LAMP sur linux, on veux maintenant configurer un `VirtualHost` pour notre application.
Lorsque vous utilisez le serveur Web Apache, vous pouvez créer des hôtes virtuels
pour encapsuler les détails de configuration et héberger plusieurs domaines à partir d'un seul serveur.
Dans ce guide, nous allons configurer un domaine appelé `ndekocode` , mais vous devez le remplacer par `votre propre nom de domaine` .

sur Ubuntu 20.04 ou plus on a un hôte virtuel activé par défaut qui est configuré pour servir les documents du `/var/www/html`.

Bien que cela fonctionne bien pour un seul site, cela peut devenir compliqué si vous hébergez plusieurs sites.
Au lieu de modifier `/var/www/html`, nous allons créer une structure de répertoires `/var/www` pour le site `ndekocode` , en laissant `/var/www/html` en place,  le répertoire par défaut `/var/www/html` servira si une demande client ne correspond à aucun autre site.
Pour créer un `VirtualHost` voici les etapes à suivre

- Créez le répertoire pour `ndekocode` comme suit:

`sudo mkdir /var/www/ndekocode`

- Ensuite, attribuez la propriété du répertoire avec la variable d'environnement `$USER` , qui fera référence à votre utilisateur système actuel :

`sudo chown -R $USER:$USER /var/www/ndekocode` et `sudo chmod 775 /var/www/ndekocode`

- Ensuite, ouvrez un nouveau fichier de configuration dans le répertoire d'Apache `sites-available`  à l'aide de votre éditeur de ligne de commande préféré. Ici, nous utiliserons nano:

`sudo nano /etc/apache2/sites-available/your_domain.conf`

Cela créera un nouveau fichier vierge. Ajoutez la configuration simple suivante avec votre propre nom de domaine Et Enregistrez et fermez le fichier lorsque vous avez terminé

```{Apache}
<VirtualHost *:80>
    ServerName ndekocode.local
    ServerAlias www.ndekocode.local
    ServerAdmin webmaster@localhost
    DocumentRoot "/var/www/ndekocode"
    <Directory "/var/www/ndekocode">
  Options FollowSymLinks
  AllowOverride all
  Require all granted
 </Directory>
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>

```

Enregistrez et fermez le fichier lorsque vous avez terminé. Ensuite, activez l'hôte virtuel Apache et réécrivez le module

- Maintenant, utilisez `a2ensite` pour activer le nouvel hôte virtuel :

`sudo a2ensite ndekocode`

- Utilisez la commande: `a2enmod rewrite` pour réécrivez le module

Vous souhaiterez peut-être désactiver le site Web par défaut installé avec Apache. Ceci est nécessaire si vous n'utilisez pas de nom de domaine personnalisé, car dans ce cas, la configuration par défaut d'Apache remplacerait votre hôte virtuel. Pour désactiver le site Web par défaut d'Apache, tapez:

`sudo a2dissite 000-default`

- Pour vous assurer que votre fichier de configuration ne contient pas d'erreurs de syntaxe, exécutez la commande suivante :

`sudo apache2ctl configtest`

- Pour ajouter votre `VirtualHost` aux hosts global il va falloir l'indiquer dans le fichier `/etc/hosts` et ajouter la ligne correspondant à l'adresse de votre `VirtualHost` avec `sudo nano /etc/hosts`

```{HOSTS}
127.0.0.1   localhost
127.0.0.1   bongobtrust.local
127.0.0.1   ndekocode.local

# The following lines are desirable for IPV6 capable hosts
::1     ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allroutes

```

- Enfin, redémarrez le service Apache pour mettre en œuvre les modification avec la commande:

`sudo systemctl reload apache2`

Votre nouveau site Web est maintenant actif, mais la racine Web est toujours vide. Créez un fichier à cet emplacement pour tester que l'hôte virtuel fonctionne comme prévu: `/var/www/ndekocode/index.html` ou `/var/www/ndekocde/index.php`

- Inclure le contenu suivant dans ce fichier :

```{HTML}
<html>
  <head>
    <title>ndekocde website</title>
  </head>
  <body>
    <h1>Hello World!</h1>

    <p>This is the landing page of <strong>ndekocode</strong>.</p>
  </body>
</html>
```

Enregistrez et fermez le fichier, puis accédez à votre navigateur et accédez au nom de domaine ou à l'adresse IP de votre serveur
`http://server_domain_or_IP` par exemple `http://ndekocode.local`
