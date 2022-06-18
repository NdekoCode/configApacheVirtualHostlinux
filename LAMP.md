# Comment installer la pile Linux, Apache, MySQL, PHP (LAMP) sur Ubuntu

Une pile `"LAMP"` est un groupe de logiciels `open source` qui sont généralement installés ensemble afin de permettre à un serveur d'héberger des sites Web dynamiques et des applications Web écrites en PHP. **Ce terme est un** acronyme qui représente le système d' **exploitation** **L** `Linux` avec le serveur web **A** `Apache`. Les données du site sont stockées dans une base de données **M** **`MySQL`** et le contenu dynamique est traité par **P** **`PHP`**.

## Étape 1 - Installation d'Apache

ommencez par mettre à jour le cache du gestionnaire de packages. Si c'est la première fois que vous utilisez `sudo` cette session: `sudo apt update` Ensuite, installez Apache avec : `sudo apt install apache2`
Vous serez invité à confirmer l'installation d'Apache. Validez en appuyant sur Y, puis ENTER.

Une fois l'installation terminée, vous devrez ajuster les paramètres de votre pare-feu pour autoriser le trafic HTTP.
L'outil de configuration du pare-feu par défaut d'Ubuntu s'appelle (UFW)
Il a différents profils d'application que vous pouvez exploiter. Pour répertorier tous les profils d'application UFW actuellement disponibles, exécutez cette commande : `sudo ufw app list`

```{SHELL}
# Output
Available applications:
  Apache
  Apache Full
  Apache Secure
  OpenSSH

```

Voici ce que chacun de ces profils signifie :

- **Apache** : ce profil ouvre uniquement le port `80`(trafic Web normal et non chiffré).
- **Apache Full** : ce profil ouvre à la fois le port `80`(trafic Web normal non crypté) et le port `443`(trafic crypté TLS/SSL).
- **Apache Secure** : Ce profil ouvre uniquement le port `443`(trafic crypté TLS/SSL).
Pour l'instant, il est préférable de n'autoriser que les connexions sur le port 80, puisqu'il s'agit d'une nouvelle installation d'Apache et que vous n'avez pas encore de certificat TLS/SSL configuré pour autoriser le trafic HTTPS sur votre serveur.
Pour autoriser uniquement le trafic sur le port `80` du profile `Apache`, utilisez: `sudo ufw allow in "Apache"` Vérifiez le changement avec : `sudo ufw status`

```
Output
Status: active

To                         Action      From
--                         ------      ----                           
Apache                     ALLOW       Anywhere                         
Apache (v6)                ALLOW       Anywhere (v6)
```

Le trafic sur le port `80`est désormais autorisé via le pare-feu.
Pour acceder à la page d'apache aller sur `http://your_server_ip`

## Étape 2 - Installation de MySQL

Maintenant que vous avez un serveur Web opérationnel, vous devez installer le système de base de données pour pouvoir stocker et gérer les données de votre site. MySQL est un système de gestion de base de données populaire utilisé dans les environnements PHP
Pour acquérir et installer ce logiciel il vaut utiliser:

`sudo apt install mysql-server`

Lorsque vous y êtes invité, confirmez l'installation en tapant `Y`, puis `ENTER`.

Une fois l'installation terminée, il est recommandé d'exécuter un script de sécurité préinstallé avec MySQL. Ce script supprimera certains paramètres par défaut non sécurisés et verrouillera l'accès à votre système de base de données. Démarrez le script interactif en exécutant :

`sudo mysql_secure_installation`

Cela vous demandera si vous souhaitez configurer le `VALIDATE PASSWORD PLUGIN`.
Répondez `Y`par oui, ou quoi que ce soit d'autre pour continuer sans activer.

```{MYSQL}
VALIDATE PASSWORD PLUGIN can be used to test passwords
and improve security. It checks the strength of password
and allows the users to set only those passwords which are
secure enough. Would you like to setup VALIDATE PASSWORD plugin?

Press y|Y for Yes, any other key for No:
```

Si vous répondez « oui », il vous sera demandé de sélectionner un niveau de validation du mot de passe. Gardez à l'esprit que si vous entrez `2`pour le niveau le plus fort, vous recevrez des erreurs lorsque vous tenterez de définir un mot de passe qui ne contient pas de chiffres, de lettres majuscules et minuscules et de caractères spéciaux :

```{SHELL}
There are three levels of password validation policy: LOW Length >= 8 MEDIUM Length >= 8, numeric, mixed case, and special characters STRONG Length >= 8, numeric, mixed case, special characters and dictionary file Please enter 0 = LOW, 1 = MEDIUM and 2 = STRONG: 1
```

Que vous choisissiez ou non de configurer le `VALIDATE PASSWORD PLUGIN`, votre serveur vous demandera ensuite de sélectionner et de confirmer un mot de passe pour l' utilisateur **racine** MySQL . Cela ne doit pas être confondu avec la **racine du système** .
Même si la méthode d'authentification par défaut pour l'utilisateur racine MySQL n'implique pas l'utilisation d'un mot de passe, **même lorsqu'il est défini** , vous devez définir ici un mot de passe fort comme mesure de sécurité supplémentaire.
Si vous avez activé la validation du mot de passe, vous verrez la force du mot de passe root que vous venez de saisir et votre serveur vous demandera si vous souhaitez continuer avec ce mot de passe. Si vous êtes satisfait de votre mot de passe actuel, saisissez `Y`"oui" à l'invite :

```{SHELL}
Estimated strength of the password: 100 
Do you wish to continue with the password provided?(Press y|Y for Yes, any other key for No) : y
```

Pour le reste des questions, appuyez `Y` et appuyez sur la touche `ENTER` à chaque invite. Cela supprimera certains utilisateurs anonymes et la base de données de test, désactivera les connexions `root` à distance et chargera ces nouvelles règles afin que `MySQL` respecte immédiatement les modifications que vous avez apportées.
Lorsque vous avez terminé, testez si vous pouvez vous connecter à la console MySQL en tapant :

`sudo mysql`

```{MYSQL}
Output
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 10
Server version: 8.0.28-0ubuntu4 (Ubuntu)

Copyright (c) 2000, 2022, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> 
```

Notez que vous n'avez pas eu besoin de fournir un mot de passe pour vous connecter en tant qu'utilisateur roo
Pour une sécurité accrue, il est préférable d'avoir des comptes d'utilisateurs dédiés avec des privilèges moins étendus configurés pour chaque base de données, surtout si vous prévoyez d'avoir plusieurs bases de données hébergées sur votre serveur.

## Étape 3 - Installation de PHP

PHP est le composant de notre configuration qui traitera le code pour afficher le contenu dynamique à l'utilisateur final. En plus du `php` package, vous aurez besoin de `php-mysql` ,qui est un module PHP qui permet à PHP de communiquer avec des bases de données basées sur MySQL.
Vous devrez également activer `libapache2-mod-php` qui est un module Apache pour gérer les fichiers PHP.
Les packages PHP principaux seront automatiquement installés en tant que dépendances.
Pour installer ces packages, exécutez la commande suivante :

`sudo apt install php libapache2-mod-php php-mysql`

À ce stade, votre pile LAMP est pleinement opérationnelle,
La pile LAMP est alors installée. Cependant la plupart des scripts PHP utilisent des modules de PHP pour bénéficier de certaines fonctionnalités.
Voici comment installer les modules les plus courants :

`sudo apt install php-curl php-gd php-intl php-json php-mbstring php-xml php-zip`

Description des paquets :

- Le paquet [apache2](apt://apache2 "apt://apache2") installe le serveur HTTP [Apache 2](https://doc.ubuntu-fr.org/apache2 "apache2") (c'est une dépendance de libapache2-mod-php).

- Le paquet [php](apt://php "apt://php") méta-paquet permettant d'installer au moins un interpréteur [PHP](https://doc.ubuntu-fr.org/php "php") (aussi installé ici en dépendance de libapache2-mod-php).

- Le paquet [libapache2-mod-php](apt://libapache2-mod-php "apt://libapache2-mod-php") module d'Apache (on peut aussi utiliser PHP en ligne de commande ou indépendamment en FastCGI, ce qui le rend plus performant mais plus compliqué à mettre en place)

- Le paquet [mysql-server](apt://mysql-server "apt://mysql-server") installe le serveur de bases données [MySQL](https://doc.ubuntu-fr.org/mysql "mysql").

- Le paquet [mariadb-server](apt://mariadb-server "apt://mariadb-server") installe le serveur de base données [MariaDB](https://doc.ubuntu-fr.org/mariadb "mariadb").

- Le paquet [php-mysql](apt://php-mysql "apt://php-mysql") installe les module permettant d'utiliser MySQL ou MariaDB avec PHP

Une fois les paquets installés, ouvrez un des liens suivants dans votre navigateur :

- [http://127.0.0.1/](http://127.0.0.1/ "http://127.0.0.1/")
- [http://localhost](http://localhost/ "http://localhost")
Si le message « It works! » s'affiche, votre serveur Lamp est correctement installé.
 Mais avant de tester votre configuration avec un script PHP, il est préférable de configurer un hôte virtuel Apache approprié pour contenir les fichiers et dossiers de votre site Web.
