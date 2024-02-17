# Exercice 2

## Présentation 

L'architecture des containers est la suivante :

- Un container nginx pour l'équilibreur de charge
- Deux containers faisant office de serveur web (nginx + wordpress)
- Un container pour la base de données mariadb

Ici seul le container du load-balancer est ouvert vers l'hôte (ports 80 et 443) et joue le rôle de proxy inversé afin de distribuer le trafic vers les serveurs web.

## Déploiement

Une grande partie de la configuration est automatisée.
Cloner le repo et modifier les valeurs :
Il vous faudra modifier **tp2.cours-datascientest-devops.cloudns.be** par votre nom de domaine dans les fichiers suivants :
- Load-balancer/lb.conf
- WP/wp.conf

Déployer ensuite en lançant la commande : ``` vagrant up ```
Les différents containers seront créés et les packages nécéssaires seront installés.

## To do

### Base de données

Exécuter les commandes suivantes :

``` vagrant ssh db ```
``` sudo mysql_secure_installation ``` 

- Création de la base de donnée et des users :
``` sudo mariadb -u root ```
``` CREATE DATABASE wordpress; ```
``` CREATE USER 'wordpress'@'192.168.10.11' IDENTIFIED BY '0000'; ```
``` CREATE USER 'wordpress'@'192.168.10.12' IDENTIFIED BY '0000'; ```
``` GRANT ALL PRIVILEGES ON wordpress.* TO 'wordpress'@'192.168.10.11'; ```
``` GRANT ALL PRIVILEGES ON wordpress.* TO 'wordpress'@'192.168.10.12'; ```
``` FLUSH PRIVILEGES; ```
``` EXIT; ```

``` sudo systemctl restart mariadb ```
``` exit ```

### Installation de wordpress

Sur votre navigateur, se rendres sur votre site et suivre les étapes d'installation de wordpress.

### Modification du fichier home.html de wordpress

Wordpress 1 :
``` vagrant ssh w1 ```
``` sudo nano /var/www/html/wordpress/wp-content/themes/twentytwentyfour/templates/home.html ```
- Ajouter ceci tout en haut du fichier :
``` <h1 style="text-align:center;">Server 1</h1> ```
- Enregistrer et quitter l'éditeur de fichier
- Sortir du container
``` exit ```

Wordpress 2 :
``` vagrant ssh w2 ```
``` sudo nano /var/www/html/wordpress/wp-content/themes/twentytwentyfour/templates/home.html ```
- Ajouter ceci tout en haut du fichier :
``` <h1 style="text-align:center;">Server 2</h1> ```
- Enregistrer et quitter l'éditeur de fichier
- Sortir du container
``` exit ```

### Passage en https

Se connecter sur le container lb :
``` vagrant ssh lb ```
Lancer le certbot :
``` sudo certbot --nginx -d <votre-nom-de-domaine> ```
Relancer nginx :
``` sudo systemctl restart nginx ```

Certbot modifiera automatiquement la configuration nginx

### SSL offloading

Modifier le fichier wp-config sur les serveurs wordpress :
Wordpress 1 :
``` vagrant ssh w1 ```
``` sudo nano /var/www/html/wordpress/wp-config.php ```
- Décommenter cette partie en enlevant **/*** et ***/** :
``` //Fix pour SSL offloading: LB 443 vers Nginx 80
/*
if (isset($_SERVER['HTTP_X_FORWARDED_PROTO']) && $_SERVER['HTTP_X_FORWARDED_PROTO'] == 'https') {
    $_SERVER['HTTPS'] = 'on';
}
*/
 ```
- Enregistrer et quitter l'éditeur de fichier
- Sortir du container
``` exit ```

Wordpress 2 :
``` vagrant ssh w2 ```
``` sudo nano /var/www/html/wordpress/wp-config.php ```
- Décommenter cette partie en enlevant **/*** et ***/** :
``` //Fix pour SSL offloading: LB 443 vers Nginx 80
/*
if (isset($_SERVER['HTTP_X_FORWARDED_PROTO']) && $_SERVER['HTTP_X_FORWARDED_PROTO'] == 'https') {
    $_SERVER['HTTPS'] = 'on';
}
*/
 ```
- Enregistrer et quitter l'éditeur de fichier
- Sortir du container
``` exit ```

### Normalement tout devrait fonctionner

