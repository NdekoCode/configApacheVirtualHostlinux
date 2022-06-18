# Comment installer la pile Linux, Apache, MySQL, PHP (LAMP) sur Ubuntu

Une pile `"LAMP"` est un groupe de logiciels `open source` qui sont généralement installés ensemble afin de permettre à un serveur d'héberger des sites Web dynamiques et des applications Web écrites en PHP. **Ce terme est un** acronyme qui représente le système d' **exploitation** **L** `Linux` avec le serveur web **A** `Apache`. Les données du site sont stockées dans une base de données **M** **`MySQL`** et le contenu dynamique est traité par **P** **`PHP`**.

- Étape 1 - Installation d'Apache
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
