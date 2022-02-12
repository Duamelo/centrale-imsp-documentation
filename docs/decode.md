# Présentation du module de décodage

Ici, il s'agit de vous présenter globalement ce que nous avons fait dans cette partie du projet afin d'avoir les données brute dans un format lisible et exploitable.

## Contexte

Étant donné que les données brutes recueillies depuis la centrale sont dans un format hexadécimal (incompréhensible à première vue), il nous a fallu écrire un module de décodage dont le but est d'analyser le fichier brute de données et de transformer ces données hexadécimales en données ASCII.

## Module de décodage des fichiers brutes

Ce module contient principalement deux sous-modules: 

- Le module de parsage.
- Module de décodage proprement dit.

Après analyse des fichiers brutes, (vérification des répétitions, régularité, constituant de l'entête) contenant entre autre :

- entêtes (description de la table, heure d'enregistrement, information sur les colonne, soit les sorties des capteurs);
- données brutes mesurées;

nous avons écrit le sous-module de parsage.

### Module de parsage parse.c

Ce module détecte l'entête, formate un certain nombre d'information, récupère les informations utiles au décodage même, c'est-à-dire :

- la description de la table (nom, date d'enegistement, fréquence, ...);
- la description d'une ligne d'enregistrement (nombre de colonne, type de chaque donnée par colonne, unité, ...).

#### Utilité

Avec ces informations, nous savons que :

- les données sont enregistrées à partir d'une date t;
- avec une fréquence f;
- qu'un enregistrement contient c colonnes;
- chaque colonne étant de type c_type donc occupant c_t octet en mémoire.

Toujours avec ces informations, nous connaissons :

- l'id de la station;
- la taille totale d'un enregistrement;
- le nombre d'octet occupé par une colonne de l'enregistrement;

ce qui nous permet de récupérer à chaque fois un enregistrement brute, de le parser et de le décoder en conséquence.


Dans la partie description de la table, nous prenons la date et la fréquence d'enregistement. Avec la fonction **arrondi_date**, on arrondit la date dans le but d'avoir des temps d'enregistrement cohérents.

En utilisant la fréquence d'enregistrement, on incrémente le timestamp pour chaque enregistrement en utilisant la fonction **increment_date**.

La fonction **parsevalue** prend le retour de la fonction **parseheader** qui est un tableau de structure...
Elle nous renvoie une succession de fichiers.
Chaque fichier représentant une colonne, chacune contenant la date, les données et l'id adéquate.

### Module de décodage decode.c

Ce module est appelé dans le module de parsage à une étape bien précise.

On appelle ce module sur la chaîne de caractère que **parsevalue** à couper et cette chaine correspond à une colonne.