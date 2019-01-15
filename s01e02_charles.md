# Cours O'Clock - Saison 01 Episode 02
_Premier doc en Markdown, soyez indulgents_

> /!\ __Ce sont des notes de cours, elles peuvent contenir des erreurs__ /!\

## Correction du challenge

* __Vu la veille__ : `mkdir`, `ls`, `pwd`, `cd`, `mv`, `touch`, `rm`, `unzip`, `man`, `tree`, `clear`... ?
> Info dans fiche récap' O'Clock : https://github.com/O-clock-Alumni/fiches-recap/blob/master/ldc/terminal.md

### GIT

> Fiche récap' O'Clock : https://github.com/O-clock-Alumni/fiches-recap/blob/master/ldc/git.md

* __Cmd__ `git clone` https://copier_le_lien_du_dépot_en_https
    * Sert à cloner un dépôt GitHub sur sa machine/serveur
* __Id__ Même identifiants que sur GitHub
    _/!\ Git demande les identifiants pour chaque commande_

### Terminal

> __AU CURIEUX__ : Série de vidéo Youtube très, très didactique sur le terminal pour tout comprendre, un peu long mais tellement clair (en 4 parties) : https://www.youtube.com/watch?v=OMaNgQi6Fvc 

* __Cmd__ `-q` == option qui passe sous silence les informations dans le terminal mais affiche les erreurs si erreur.
* __Nav__ `cd doss/sousdoss/etc` == "aller de l'avant" // `cd ../../etc` == "aller de l'arrière" (Marianne, janvier 2019 :p) 

> /!\ Bien utiliser la touche `tab` pour naviguer dans le terminal, à combiner avec la cmd `cd`

* __Cmd__ `cat fichier.format` == catalogue, /!\ pour les fichiers long !
* __Cmd__ `head fichier.format` == Les premières lignes du fichier
* __Cmd__ `tail fichier.format` == Les dernières lignes du fichier (sert pour lire les erreurs)

> `nom_du_processus chemin_vers_le_fichier/mon_fichier.format` __Créer un fichier directement avec le logiciel adéquat__ (VSC par exemple) au bon endroit. (Ex : `code arbo.md`)

## Général

* GUI == Interface graphique
* On peut mettre n'importe quoi dans un fichier, il faut juste ouvrir le fichier avec un logiciel adapté à son format.


## Visual Studio Code

* __Cmd__ Alt+z == _Toggle word wrap_ retour à la ligne automatique pour plus de confort de lecture
* __Cmd__ Ctrl+f == _Search & Replace_

> __HTML__ a un rôle __sémantique__ et __structurel__ pour la page. __CSS__ a un rôle de __mise en page__.


## HTML

> * __Fiche récap' O'Clock__ : https://github.com/O-clock-Alumni/fiches-recap/blob/master/html/syntaxe.md
> * __Ressources MDN__ : https://developer.mozilla.org/fr/docs/Web/HTML
> * __Bible__ : https://devdocs.io/ __ou__ https://htmlcheatsheet.com/ _(merci Gaël)_
> * __Librairie__ : Editeur Eyrolles ==> Grosse référence



* Les serveurs vont chercher directement les fichiers `index.html` dans un dossier.

* Toujours commencer un fichier html par `<!DOCTYPE html>`

> __html se *cogne* des indentations et des sauts de ligne c'est un code minifié__

> __Le html est structuré en encapsulation comme les poupées russes__

> __F12 dans le navigateur ouvre l'inspecteur__

* `<!-- Ceci est la forme d'un commentaire en html-->`

* Il y a deux types principaux de balises :
    * __Block__ == Crée un bloc, va à la ligne au dessus et en dessous.
    * __Inline__ == Prend la taille de son contenu et ne va pas à la ligne.

* `<head>` == Information sur la page, ne s'affiche pas dans le navigateur sans inspecteur.
    * `<title>` == Titre de la page, on le retrouve dans l'onglet/fenêtre
    * `<meta charset="UTF-8">` == Encodage, infos super claires ici : https://www.grafikart.fr/tutoriels/encodage-utf8-1000
    * __Les balises dans le `<head>` n'ont pas un ordre obligatoire__

* `<body>` == Contenu de la page
    * `<h1>` == Titre de niveau 1, souvent unique.
    * `<h2>` == Titre de niveau 2, etc pour les autres niveaux.
    * `<br>` == Saut à la ligne, balise auto-fermante
    * `<span>` == Balise universelle inline
    * `<div>` == Balise universelle block
    * `&nbsp;` == Espace inline

> Taper `!` et valider en début de fichier html pour générer la structure de base d'un document html !

### Les attributs

> __Synthaxe__ == `<nom_balise nom_attribut="patatipatata">`

* `<a href="lien.fr">Cliquez là c'est top !</a>` == Synthaxe d'un lien (seule balise inline qui peut contenir des éléments de type block)

* __Liste Attributs__
    * `target="_blank"` == Ouvrir dans un nouvel onglet
    * `href="chemin"` == Lien vers fichier, site, ancres, etc. _/!\ Lien absolu, lien relatif, etc._
    * `disable` == Désactiver
    * `lang="en"` == Langue de la page
    * `charset="UTF-8"` == Encodage de la page
    * `title="titre_ici"` == Ajoute une info-bulle
    * `id="nom_id"` == Une ancre, usage unique sur une page
    * `class="nom_class"` == Class est un id réutilisable, ça crée des classes :)

## CSS

> __Fiche récap' CSS__ : https://github.com/O-clock-Alumni/fiches-recap/tree/master/css

> __Cheatsheet__ : https://htmlcheatsheet.com/css/ (Coucou Gaël)

> __Ressource sélecteur de CSS__ : https://www.grafikart.fr/tutoriels/selecteur-css-1035


### Ou faire du CSS ?

* Ecrire en attribut dans une balise (c'est dégueulasse) == `<balise style="background-color:red;">`
* Ecrire dans le header avec la balise style == `<style>`
* Ecrire dans un fichier externe et faire le lien dans le header de la page html == `<link rel="stylesheet" href="chemin_vers_mon_fichier_css">`

### Synthaxe
```
Elements_à_styliser {
    paramètre: patati;
    paramètre: patato;
}
```
* Exemple :
```
h1 {
    text-align: center;
    font-weight: 200;
}
```

> Les règles CSS sont lues avant le contenu. 

* __/!\ class & id servent à faire des références en CSS__
    * #nom_id == pour un id
    * .nom_class == pour une class

* La mise en page est __héritée__, c'est-à-dire que la règle la plus précise l'emporte et si pas de règles, applique la règle du parent.
    * Dans le fichier CSS, si deux règles similaires : la dernière l'emporte
    * _On peut forcer le style parent avec `!important` ?_ (Maxou ?)

### Quelques propriétés

* `background-color` == couleur du fond
* `color` == couleur de la police
* `font-size` == taille de la police
* `font-weight` == épaisseur de la police
* `text-decoration` == décoration du texte (soulignage, etc.)
* `font-style` == italique, normal
* `text-align` == gestion de la disposition du texte

### Les unités

* `px` == pixel, plus petite unité d'affichage
* `em` == unité héritée du parent (3em = 3x la taille du parent)
* `rem` == relatif au root
* `%` == pourcentage de son espace pris par défaut
* `vh` == pourcentage de la hauteur disponible sur la fenêtre
* `vw` == pareil pour la largeur












