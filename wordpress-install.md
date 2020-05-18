 # Notes pour installer proprement Wordpress en installation Custom 

 ### Etape 1 : Installer l'environnement Apache2, MySQL, phpMyAdmin 

 En effet, Wordpress, c'est du PHP, du langage serveur, il nous est utile d'avoir un environnement opérationnel 
 Perso, je travaille sur Linux car j'y suis plus à l'aise avec les lignes de commandes mais vous pouvez tout aussi bien télécharger un terminal linux sur votre Windows 

#### Pour commencer, on met à jour et on télécharge Apache2 : 

`sudo apt-get update`
`sudo apt-get install apache2`

#### Ensuite, on installe le langage PHP : 

```
sudo add-apt-repository ppa:ondrej/php
sudo apt-get update
sudo apt-get install php7.4 php7.4-common php7.4-cli php7.4-mysql libapache2-mod-php7.4 php7.4-mbstring php7.4-json php7.4-xml
```

#### Puis on installe MySQL (Je vous conseille de bien suivre la démarche) :

`sudo apt-get install mysql-server`

- Si vous souhaitez permettre un accès à la base de donnée depuis l'exterieur :

Modification de la configuration de mysql => 

`sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf`

puis remplacer `bind-address = 127.0.0.1` par `bind-address = 0.0.0.0`

On redémarre mysql : `sudo service mysql restart`

Pour permettre l'accès depuis l'exterieur avec un utilisateur : `'<nom-utilisateur-externe>'@'<adresse-utilisateur-externe>'`

#### Création d'un super-utilisateur (super important !)

Nous allons créer un super-utlisateur autre que `root` afin de gérer notre serveur MySQL.

On se connecte à la console de mysql via `sudo mysql` puis on va entrer les requêtes SQL ci-dessous : 

```
CREATE USER '<nom-utilisateur>'@'localhost' IDENTIFIED BY '<mdp-de-votre-choix>';
GRANT ALL PRIVILEGES ON *.* TO '<nom-utilisateur>'@'localhost' WITH GRANT OPTION;
```

Si vous ne savez plus quel est votre nom d'utilisateur, tapez la commande `whoami` dans votre terminale.

=> <strong>Vous pourrez utiliser ce compte pour gérer vos bases depuis PMA</strong> (PhpMyAdmin).

#### Installation de PhpMyAdmin

`sudo apt-get install phpmyadmin`

A la première question on répondra `Ok` ou `Yes` (installation d'un package supplémentaire et création d'un mot de passe automatique pour PMA).

=> Quand l'install vous demande quel serveur web vous utilisez, cochez `apache2` avec la barre d'Espace ! Un astérisque * valide le choix, ensuite faites Entrée ou Ok.

Si vous vous êtes trompé suite à ce choix, après l'install, vous pouvez relancer la configuration via `sudo dpkg-reconfigure phpmyadmin`

Connectez-vous à PMA depuis l'URL de notre serveur http://votre_serveur_aws/phpmyadmin et le compte créé juste au-dessus. (En local, ce sera http://localhost/phpmyadmin/)

#### Composer 

Composer est une librairie de dépendances de PHP.

```
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
php composer-setup.php
php -r "unlink('composer-setup.php');"
sudo mv composer.phar /usr/local/bin/composer
```

## Configuration de tout ce tralala

#### DocumentRoot Apache
`DocumentRoot`, c'est le dossier contenant les fichiers du site livré par Apache.
Par exemple, dans ce serveur, c'est `/var/www/html`

Suppression du fichier `index.html` par défaut d'Apache 2
Et modification des droits d'accès du `DocumentRoot` : 

```
sudo rm -f /var/www/html/index.html
sudo chown -Rf ubuntu:www-data /var/www/html
```

#### URL Rewriting
```sudo a2enmod rewrite```

Dans /etc/apache2/apache2.conf (dans le terminale, `nano /etc/apache2/apache2.conf` pour modifier, sinon ouvrez le dans votre IDE)

```
<Directory /var/www/>
    Options Indexes FollowSymLinks
    AllowOverride All
    Require all granted
</Directory>
```

Puis on relance Apache2
```
sudo systemctl restart apache2
```

Plus d'infos => {@Link: https://github.com/O-clock-Alumni/fiches-recap/blob/master/adminsys/aws/install.md}

=====================================================================================================================================================================

# Installation custom de Wordpress 

Wordpress, c'est pratique, c'est 35% des sites du Web. Pour créer un site vitrine, c'est très rapide. Un développeur qui s'y connait bien pourrait en faire un en 2 jours. 

Rendez-vous dans `/var/www/html/`, créez un dossier `sites-wordpress` puis téléchargez le dossier wordpress (https://fr.wordpress.org/download/). Extraire le .zip et renommer le dossier extrait. 

On devient propriétaire du wordpress `sudo chown -R <nom-utilisateur>:www-data .` car www-data, c'est l'utilisateur de apache, il est généré automatiquement. 
Ensuite, il va falloir se donner des droits de lecture et d'écriture. D'écriture pour l'utilisateur, et de lecture pour les autres. 
`sudo find . -type f -exec chmod 664 {} +` : super-utilisateur recherche dans le fichier courant les fichiers (-type f) et tu exécute le changements des droits (exec 664)
`sudo find . -type d -exec chmod 775 {} +` : idem mais pour les dossier (-type d) avec des droits 775
f = file / d = directory
Pour plus d'infos : https://openclassrooms.com/fr/courses/43538-reprenez-le-controle-a-laide-de-linux/39044-les-utilisateurs-et-les-droits
Sur la bible de Wordpress : https://wordpress.org/support/article/changing-file-permissions/

On peut donc se rendre dans `http://localhost/sites-wordpress/nom-du-site/` puis cliquer sur "C'est parti !" puis... Ah. On a pas de base de données en faite. 

Pas de panique, on part sur `http://localhost/phpmyadmin/`, on rentre l'identifiant et le mdp que l'on a configuré dans MySQL. Vous vous souvenez pourquoi on a fait ça ? => 

```
CREATE USER '<nom-utilisateur>'@'localhost' IDENTIFIED BY '<mdp-de-votre-choix>';
GRANT ALL PRIVILEGES ON *.* TO '<nom-utilisateur>'@'localhost' WITH GRANT OPTION;
```
C'était pour être un super-utilisateur de de PMA ! D'où l'importance car sinon il faut passer par `root`, mais si vous ne possédez pas le mdp, c'est une galère sans nom (vous devez sentir que je parle en connaissance de cause...)

Ok, on a accès à PMA. On va dans l'onglet "Comptes d'utilisateurs", on fait "ajouter un nouvel utilisateur".

Nom d'utilisateur : celui que vous souhaitez pour la bdd (en général, le nom du site séparé par des tirets)
Nom d'hôte : dans le déroulant, on choisi "localhost" 
Mot de passe : le mdp pour accéder à la bdd

On coche la case "Créer une base portant son nom et donner à cet utilisateur tous les privilèges sur cette base."

Puis on éxécute (le bouton tout en bas :P)

Voilà, notre bdd est prête. On retourne donc sur notre `http://localhost/sites-wordpress/nom-du-site/`, et on ajoute les données propre à notre bdd, on ne touche pas au "prefix" de wordpress, on le laisse avec "wp_" car c'est comme ça. Puis on clique sur "Envoyer" et on lance l'installation. 

Ensuite, Wordpress nous demande des informations pour notre site. Le titre du site, un identifiant, un mdp etc. Je vous laise faire à votre guise ;) 

PS : Si jamais vous avez un soucis, hésitez pas à refaire les commandes 
```
sudo chown -R <nom-utilisateur>:www-data .
sudo find . -type f -exec chmod 664 {} +
sudo find . -type d -exec chmod 775 {} +
```

Si l'on retourne sur notre base de données, on peut remarquer que Wordpress a importer toutes les tables nécessaire au bon developpement de notre site Wordpress, et qu'il nous a créé le fichier `wp-config.php`, fichier qui regroupe les données relatif à notre bdd. 

IMPORTANT : Ne pushez JAMAIS votre `wp-config.php` sur votre repo en remote ! Sinon, toute personne pourra avoir accès à votre base de donnée ! 
Placez le donc dans un `.gitignore` à la racine de votre projet.

Voilà, on a accès au tableau de bord de notre Wordpress ! Et on peut voir qu'il y a des mise à jour. Mais on nous demande des informations FTP... 
Alors pour contrer ça et garder notre site sécurisé, dans notre wp-config.php, à la fin, en dessous de la constante WP_DEBUG, on ajoute : 

```
// J'ordonne à WP d'utiliser la méthode simple pour manipuler les fichiers
// Pas besoin de FTP ni de SSH : la machine est bien configurée et sécurisée
define('FS_METHOD', 'direct');
```

et on peut exécuter nos MAJ sans problème. 

#### Permaliens 

Dans notre tableau de bord, dans l'onglet "Réglages" > "Permaliens", je vous invite à choisir "Titre de la publication". 
Cela permettra de générer une URL plus propre. 

ensuite, on va se donner les droits sur le fichier `.htacess` : `sudo chmod 644 .htaccess`
Et on colle ces chemins dans ce même fichier : 
```
RewriteEngine on
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule . index.php [L]
```
Le fichier `.htaccess` est devenu universel (car sinon, si on change de nouveau les permaliens, Wordpress nous génère un nouveau .htaccess automatiquement).

#### Désactiver l'éditeur de thème 
Dans le tableau de bord, dasn "Apparence" > "Editeur de thème", vous avez la possibilité de modifier votre thème. 
Sauf que, si votre client y touche, ça risque de casser votre site. Un peu déconnant...
Par sécurité, on le désactive dans `wp-config.php`

```
// désactiver l'éditeur de thème embarqué dans le tableau de bord de Wordpress
// Pour éviter que votre client ne fasse n'importe quoi avec votre thème :D 
define('DISALLOW_FILE_EDIT', true);
```

### Liens utiles 

Télécharger Wordpress : https://fr.wordpress.org/download/
Codex (la bible) : https://codex.wordpress.org/
wp-config : https://codex.wordpress.org/fr:Modifier_wp-config.php

/!\ Si on bosse à plusieurs et qu'on veut pointer vers la même base de donnée, on rentre l'adress IP de notre hébergeur. 

#### PS 
Si jamais vous aviez besoin de changer votre identifiant wordpress, votre mdp etc. 
Dans votre bdd, choisissez la table wp_user et vous pourrez modifier à votre guise les informations que vous souhaitez. 

Dans wp_options, vous pourrez modifier vos url, votre mail_server etc. 

#### Rappel de ce qu'il faut ajouter dans notre wp-config 

```
define( 'WP_DEBUG', true ); // Permet d'afficher les erreurs, à savoir, les révisions lorsque l'on modifie un contenu
// A passer à false lorsque que l'on developpe le site


if(WP_DEBUG) { // Si le debug est true
  define('WP_DEBUG_LOG', true); // Permet d'avoir le fichier debug.log
  
  define('WP_DEBUG_DISPLAY', true); // Je demande à wp de m'afficher les messages d'erreurs 

  // Désactivation total des révisions de contenus
  define('WP_POST_REVISIONS', false);

  // Vider la corbeille
  define('EMPTY_TRASH_DAYS', 0 );  // 0 days

} else {
  // Je suis en prod, Je désactive l'installation de thème et de plugins automatique
  define('DISALLOW_FILE_MODS', true);

  // JE limite le nombre de versions à 5 (pour éviter de surcharger la BDD quand on revoit un article car tout changement est sauvegardé)
  define('WP_POST_REVISIONS', 5);

  // La corbeille pour le client durera 15 jours avant d'être définitivement supprimé.
  define('EMPTY_TRASH_DAYS', 15 );  // 15 days
}

// J'ordonne à WP d'utiliser la méthode simple pour manipuler les fichiers
// Pas besoin de FTP ni de SSH : la machine est bien configurée et sécurisée
define('FS_METHOD', 'direct');

// désactiver l'éditeur de thème embarqué dans le tableau de bord de Wordpress (vu que l'on possède VSC)
// Pour éviter que votre client ne fasse n'importe quoi avec votre thème :D 
define('DISALLOW_FILE_EDIT', true);



/* C’est tout, ne touchez pas à ce qui suit ! Bonne publication. */

/** Chemin absolu vers le dossier de WordPress. */
if ( ! defined( 'ABSPATH' ) )
  define( 'ABSPATH', dirname( __FILE__ ) . '/' );

/** Réglage des variables de WordPress et de ses fichiers inclus. */
require_once( ABSPATH . 'wp-settings.php' );

```

### Par quoi commencer ?

Comprendre le fonctionnement de Wordpress : https://www.rarst.net/wordpress/wordpress-core-load/

Tout d'abord, en naviguant un peu dans nos dossiers, on peut voir que celui qui va nous intéresser le plus est le dossier `wp-content` qui regroupe nos thèmes, nos plugins etc
On peut voir qu'il y a un fichier `index.php` contenant un commentaire "silence is golden". C'est tout simplement pour éviter que ceux qui vont visiter notre site aille fouiller dans nos fichier si ils tapent nom-du-site/wp-content/ dans l'URL. Apache redirige l'utilisateur sur une page blanche grace au `index.php`.

En tant que développeur, on aura juste à modifier `wp-config.php`, `.htaccess` et le dossier `wp-content`. tout le reste est seulement pour le bon fonctionnement du coeur de wordpress. 




#### Composer : téléchargement du repo wordpress

Alors pour commencer, on va tout effacer SAUF `.htaccess`, `wp-config.php` et l'`index.php` qui est à la racine

- Ouvrir `index.php` et ajouter /wp/ dans le require, pour nous permettre d'être redirigé correctement :

``` require __DIR__ . '/wp/wp-blog-header.php'; ```

- Dans la bdd > wp_options > modifier siteurl > ajouter /wp/ à la fin de l'URL

- Dans le terminale : `composer require johnpbloch/wordpress`

Ce repo nous télécharge une architecture propre et à jour de Wordpress.
https://packagist.org/packages/johnpbloch/wordpress 
Pour info : Packagist est un site regroupant pleins de repo Composer, donc de packages PHP installable avec Composer
On peut le comparer à NPM (packages javascript) mais pour PHP.

Cela nous génère /vendor/ et un dossier /wordpress/ qui contient tous les fichiers que nous avons effacer, ainsi qu'un `composer.lock` et un `composer.json`
Comme ça, si on versionne notre projet, on pourra versionner seulement les fichiers hors wp et vendor.

- Dans notre `composer.json` : on ajoute un extra 

```
{
    "require": {
        "johnpbloch/wordpress": "^5.4"
    }, 
    "extra": {
        "wordpress-install-dir": "wp"
    }
}
```
Entre autre, on demande à composer que pour trouver Wordpress, faut aller dans /wp/

On supprime le dossier `vendor`, on supprime le dossier `wordpress` puis on fait `composer install`
Cela nous génère de nouveau nos dossiers, MAIS le dossier /wordpress/ est désormais sous le nom /wp/

Alors après, vous pouvez laisser /wordpress/ comme nom de dossier. Mais /wp/ ça fait plus joli dans l'URL. Si vosu préférez /wordpress/, n'oubliez pas de modifier vos redirections.


#### Problème de langage 

Si jamais vous avez des soucis de choix de langue : par exemple, après avoir installer mon package, mon administration wordpress est revenu en anglais, et là, impossible de re-changer la langue :scream: 

Dans `wp-config.php`, ajouter la constante WPLANG avec le préfixe français `define('WPLANG', 'fr_FR');`
Puis, rendez-vous sur le sites de wordpress.org et re-téléchargez Wordpress (ou récupérer le .zip que nous avions au début de ce tuto si vous l'avez concervé).
Extraire le .zip, rendez-vous dans `/wp-content/` et récupérer le dossier `/languages/` puis collez le dans le `/wp-content/` de notre projet. 

Et hop, ça refonctionne ! 

Lien de la technique : https://www.youtube.com/watch?v=V2a2CDC7F9Y




