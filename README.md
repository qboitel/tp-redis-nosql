# TP REDIS

## Partie 1

Pour monter un cluster Redis, j'ai choisi de passer par Docker pour gagner en simplicité et modularité.
J'ai donc écrit un fichier docker compose qui contient 6 instances de redis, un conteneur pour créer le cluster Redis des 6 instances et un conteneur avec Redis Insight pour accéder à de la data et une visualisation de mon cluster.

Pour configurer mes instances Redis, j'ai créé un dossier pour chaque instance avec dedans le fichier de configuration : redis.conf

Ce fichier redis.conf contient la base de la configuration de mes instances Redis avec le port, un bool pour indiquer si le mode cluster est activé, le bind IP de l'instance, etc.

Pour lancer le cluster Redis, il suffit de taper la commande suivante :

```bash
$ docker compose up
```

## Partie 2

Dans cette partie, il s'agit de montrer l'insertion de données dans Redis directement depuis la CLI.

J'ai donc choisi d'insérer des données de type String, Lists et Sets.

PS : Pour faire l'insertion des données dans Redis, il faut au préalable se connecter à l'instance souhaitée, et lancer la CLI Redis (redis-cli) avec la commande suivante :

```bash
redis-cli -p 7000 # Il faut ici indiquer le port de l'istance Redis choisie tel que défini dans le fichier redis.conf
```

### Insertion d'une String

Pour insérer une string dans Redis, on utilise la commande SET :

```bash
SET test_string "Hello World"
```

Pour vérifier que la string est bien insérée dans Redis, on peut vérifier que la clé test_string est bien présente : 

```bash
GET test_string
```

### Insertion d'une List

Pour insérer une liste, on utilise LPUSH :

```bash
LPUSH test_list "World"
LPUSH test_list "Hello"
```

Pour vérifier que la data est bien présente, on utilise LRANGE :

```bash
LRANGE test_list 0 -1 # -1 à la fin pour récupérer toutes les valeurs de la liste
```

### Insertion d'un Set

Poru insérer un set, on utilise la commande SADD :

```bash
SADD test_set "Hello"
SADD test_set "World"
```

Pour vérifier la présence du set dans Redis, on utilise SMEMBERS :

```bash
SMEMBERS test_set
```

## Partie 3

Pour cette partie sur l'intégration de Redis dans un projet, je vais présenter un projet sur une API de contrôle d'accès d'un système de billetterie.

Dans cette exemple, l'API est développée en PHP 8 avec Api Platform et Symfony.
Redis est lui utilisé en tant que cache pour améliorer la performance de cette API lors du contrôle d'accès d'un festival par exemple (qui voit un nombre allant de 50K à 100K de contrôle de billets en une journée).

Redis, ici sert à mettre en cache  les listings de billets à contrôler, le listing est récupéré en amont par le personnel pour être charger dans les outils de contrôle (Scanneur de code-barres, etc.). Dans ce cas précis Redis en mode cache est très bénéfique car une cinquantaine de personnes requêtent en même temps les mêmes infos.

Le but étant de contruire le cache pour éviter de faire les requêtes à la BDD au maximum. Redis sert aussi à conserver en cache les configurations des différents outils de contrôle pour pouvoir, encore une fois éviter le requétage de la BDD en permanance.

Redis permet donc de subir une plus forte charge, en permettant de rediriger une partie du traffic sur le cache déjà construit pour libérer la BDD pour les requêtes nécessitant réellement celle-ci.

## Partie 4

Comme on vient de le voir, l'intégration de Redis dans une API permet de répartir la charge et de réduire les contraintes sur la BDD en construisant un cache performant évitant donc les requêtes sur la BDD.

Mais Redis peut aussi servir en tant que BDD, on peut aussi bien imaginer Redis stocker des sessions d'utilisateurs, vu qu'il permet de stocker bien des types de données (Strings, Lists, Sets, Hashes, etc.).

Une chose à savoir est que Redis est facilement scalable horizontalement (sûrtout avec Docker) mais plus difficilement en vertical. Redis propose une performance optimale sur les IO de données, il est meilleur d'une BDD dîte classique (Relationnelle tel que MySQL, MariaDB ou PostgrèSQL). 

On peut donc vite imaginer mélanger un système de base de données avec un Redis pour les données requêtées souvent (données utilisateurs, sessions, etc.) et une base de données plus traditionnelle (Relationelle ou NoSQL) comme MariaDB ou PostgrèSQL.

