# Installation de laravel sous  Linux(LAMP)

Laravel est un framework PHP open source qui peut être utilisé pour développer des applications Web plus facilement et plus rapidement grâce à des fonctionnalités intégrées. Il est basé sur le framework Symfony et suit le modèle architectural MVC(modèle-vue-contrôleur).

Laravel est livré avec un riche ensemble de fonctionnalités, notamment Artisan, le mappage objet-relationnel, le moteur de modèle, l'architecture MVC, les tests unitaires et le système de migration de base de données qui augmenteront la vitesse de développement Web.

## Etape 1: Mettez à jour votre système

```{SHELL}
apt-get update -y
apt-get upgrade -y
```

## Étape 2 : Installer La pile LAMP à partir de [LAMP](./LAMP.md)

Une fois tous les packages installés, démarrez le service Apache et autorisez-le à démarrer après le redémarrage du système à l'aide de la commande suivante :

```{SHELL}
systemctl start apache2
systemctl enable apache2
```

## Etape 3: Installer Composer

`Composer` est un gestionnaire de packages pour le langage de programmation PHP qui peut être utilisé pour gérer les dépendances du logiciel PHP et des bibliothèques requises.
Vous pouvez télécharger et installer Composer avec la commande suivante :

```{SHELL}
curl -sS https://getcomposer.org/installer | php
mv composer.phar /usr/local/bin/composer
chmod +x /usr/local/bin/composer
```

Ensuite, vérifiez la version installée de Composer avec la commande suivante :

`composer --version`

## Étape 4 : Installer le framework Laravel

Vous pouvez installer les applications Laravel à l'aide de la commande `composer create-project`. Cette commande est utilisée pour démarrer de nouvelles applications basées sur des frameworks et des systèmes de gestion de contenu existants.

La racine du document par default pour `Apache` c'est le dossier `/var/www/html`, `/var/www/html` n’est que le dossier racine par défaut du serveur Web. Vous pouvez modifier ce dossier pour en faire le dossier de votre choix en modifiant votre fichier `apache.conf` (généralement situé dans `/etc/apache/conf` ) et en modifiant l’attribut `DocumentRoot`.

Nous pour installer laravel on va le faire dans le dossier `www`, Le dossier `www` est ce qu’on appelle `un lien symbolique`.

Cela pointe vers le dossier `public_html` et est généralement utilisé comme raccourci dans les scripts `cgi` pour le chemin. Au lieu d’utiliser le chemin `/home/yourusername/public_html`, vous pouvez utiliser `/home/yourusername/www`.
Le répertoire `www` est simplement un lien symbolique vers le répertoire `public_html`.

Etant donnée que toutes nos configurations vont se faire en `local`, je vais commencer par modifier les droits d'accés ou les privilèges du dossier `/var/www` car c'est dans ce dossier que je vais disposer tous les dossier de mes applications Laravel mais en ligne pour un vrais serveur sachez que ce dossier `www` est à traiter avec pincete.
Pour commencer on modifier les privilèges avec:

```{SHELL}
sudo chmod -R 775 /var/www
sudo chmod -R 775 /var/www/*

sudo chown -R $USER:www-data /var/www
sudo chown -R $USER:www-data /var/www/*

systemctl restart apache2

```

Etant donné que c'est une configuration en local, je choisis cette methode pour eviter l'erreur Laravel:

```{Laravel}
The stream or file "/var/www/NameOfMyAppLaravel/storage/logs/laravel.log" could not be opened in append mode: failed to open stream: Permission denied code example
```

Mais dans un serveur en ligne on procède on ne modifie pas les privilèges du dossier `www` mais plutot des dossiers `storage` et `bootstrap/cache` du projet Laravel et pour cela on execute les commandes:

```{SHELL}
chmod -R 775 storage
chmod -R 775 bootstrap/cache
sudo chown -R $USER:www-data storage
sudo chown -R $USER:www-data bootstrap/cache

```

Pour installer maintenant `Laravel` avec notre configuration local, Tout d'abord deplacez-vous vers le dossier `/var/www` puis installez le avec `composer`

```{SHELL}
cd /var/www/html
composer create-project laravel/laravel NameOfMyAppLaravel --prefer-dist
```

Si vous voulez installer une version specifique de Laravel Utiliser la commande suivante

```{SHELL}
composer create-project laravel/laravel="5.*" myProject
```

Dans le code ci-dessus, nous disons que nous voulons installer la version 5.* quelque chose de Laravel, comme par exemple: `5.1`,`5.4`, `5.5`, `5.6`,..., `5.X` 

```{SHELL}
composer create-project laravel/laravel="8.*" myProject
```

Ci dessous, on dit que l'on veut installer la version 8.X
Une fois l'installation terminée, vous devriez voir la sortie suivante :

```{SHELL}
Creating a "laravel/laravel" project at "./laravelapp"
Installing laravel/laravel (v7.6.0)
- Installing laravel/laravel (v7.6.0): Loading from cache
Generating optimized autoload files
> Illuminate\Foundation\ComposerScripts::postAutoloadDump
> @php artisan package:discover --ansi
Discovered Package: facade/ignition
Discovered Package: fideloper/proxy
Discovered Package: fruitcake/laravel-cors
Discovered Package: laravel/tinker
Discovered Package: nesbot/carbon
Discovered Package: nunomaduro/collision
Package manifest generated successfully.
31 packages you are using are looking for funding.
Use the `composer fund` command to find out more!
> @php artisan key:generate --ansi
Application key set successfully.
```

Ensuite, deplacez-vous dans le répertoire `NameOfMyAppLaravel` et exécutez la commande suivante pour vérifier que tous les composants ont été installés avec succès :

```{SHELL}
cd laravelapp
php artisan
```

Vous devriez voir la sortie suivante :

```{SHELL}
Laravel Framework 7.11.0
Usage:
command [options] [arguments]
Options:
-h, --help Display this help message
-q, --quiet Do not output any message
-V, --version Display this application version
--ansi Force ANSI output
--no-ansi Disable ANSI output
-n, --no-interaction Do not ask any interactive question
--env[=ENV] The environment the command should run under
-v|vv|vvv, --verbose Increase the verbosity of messages: 1 for normal output, 2 for more verbose output and 3 for debug

```

 Ensuite, modifiez le propriétaire du répertoire laravelapp et accordez les autorisations appropriées au répertoire de stockage avec la commande suivante :

```{SHELL}
sudo chown -R www-data:www-data /var/www/NameOfMyAppLaravel
sudo chmod -R 775 /var/www/NameOfMyAppLaravel/storage
```

Cela permet d'eviter l'erreur:

```{Laravel}
The stream or file "/var/www/NameOfMyAppLaravel/storage/logs/laravel.log" could not be opened in append mode: failed to open stream: Permission denied code example
```

## Étape 5 : Configurer Apache pour servir l'application Laravel

Pour cela referer vous à [la configuration d'un `VirtualHost`](./README.md) qui est faite dans ce meme tutoriels au lien indiquer

## Étape 6 : Accéder à l'application Laravel

À ce stade, votre application Laravel est installée et configurée. Maintenant, ouvrez votre navigateur Web et tapez l'URL <http://laravel.example.com> comme par exemple <http://NameOfMyAppLaravel.local>. Vous devriez voir la page par défaut de Laravel sur votre écran de navigateur.

Toutes nos félicitations! vous avez installé avec succès le framework Laravel sur Ubuntu
