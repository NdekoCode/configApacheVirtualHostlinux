# phpMyAdmin

**[phpMyAdmin](https://www.phpmyadmin.net)** est une interface web en PHP pour administrer à distance les [SGBD](https://doc.ubuntu-fr.org/sgbd) [MySQL](https://doc.ubuntu-fr.org/mysql) et [MariaDB](https://doc.ubuntu-fr.org/mariadb).

Il permet d'administrer les éléments suivants :

- les bases de données

- les tables et leurs champs (ajout, suppression, définition du type)

- les index, les clés primaires et étrangères

- les utilisateurs de la base et leurs permissions

- importer ou exporter les données dans divers formats (CSV, XML, PDF, OpenDocument, Word, Excel et LaTeX)

## Pré-requis

Avoir installé les paquets suivants :

- le SGBD [MySQL](apt://mysql-server) ou [MariaDB](apt://mariadb-server)

- le serveur web [apache2](apt://apache2)

S'être assuré que le serveur web fonctionne et que les scripts PHP sont correctement interprétés.(Voir [LAMP](./LAMP.md))

## Installation via les dépôts

Il est recommandé d'installer phpMyAdmin depuis le site officiel pour avoir une installation fonctionnelle et de beneficier des mises à jour des sécurité
Soit en ligne de commande : `sudo apt install phpmyadmin`
Lors de l'installation, il vous sera posé quelques questions auxquelles il faut répondre avec soin :

- Créer la base de données phpmyadmin : **oui**
- Définir un mot de passe pour l'utilisateur MySQL phpmyadmin :
- L'écran suivant demandera la confirmation de ce mot de passe
- Indiquer le mot de passe de l'utilisateur MySQL « root » tel que défini à l'installation de mysql-server :
- Choisir le serveur web à configurer automatiquement (utiliser les flèches du clavier ou la touche _tab_ pour se déplacer et la barre d'espace pour sélectionner/désélectionner), Ulitiser `Apache2` si vous avez installez en suivant le tutos sur [LAMP](LAMP.md)
phpMyAdmin devrait être accessible à l'adresse <http://localhost/phpmyadmin>

### Erreur 404 avec Apache ou "phpmyadmin" Not Found after install on Apache, Ubuntu

Si vous avez choisis **Apache2** lors de l'installation du paquet _phpmyadmin_ ,Concernant les problèmes de non accessibilité de phpMyAdmin, Utilisez la commande suivante pour pouvoir répondre à nouveau aux questions :
`sudo dpkg-reconfigure phpmyadmin`.

Ensuite, lorsqu'on vous demande de choisir `apache2`, assurez-vous d'appuyer sur espace pendant que \[ \] apache2 est en surbrillance. Un astérisque doit apparaître entre les crochets. Appuyez ensuite sur `Entrée`. `Phpmyadmin` devrait reconfigurer et maintenant [http://localhost/phpmyadmin](http://localhost/phpmyadmin) devrait fonctionner.

Ou si malgrer tout cela ne marche pas, ouvrez votre terminal, Ce problème a été résolu en ajoutant `Include /etc/phpmyadmin/apache.conf` au fichier `/etc/apache2/apache2.conf` et en redémarrant le service.
Et donc Essaye ça

```{SHELL}
sudo ln -s /etc/phpmyadmin/apache.conf /etc/apache2/conf-available/phpmyadmin.conf
sudo a2enconf phpmyadmin.conf
sudo systemctl restart apache2
```

Si vous avez configurer un VirtualHost avec votre espace de travaille, pour créer un lien symbolique avec cela, utiliser la commande  :

`sudo ln -s /usr/share/phpmyadmin /var/www/votreDossierDuVirtualHost`
