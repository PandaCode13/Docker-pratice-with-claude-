Exercice 1 — Structure de base
Écrivez la première ligne d'un fichier docker-compose.yml qui indique la version du format utilisé (3.8).

Exercice 2 — Déclarer un service
Dans ce fichier, déclarez un service nommé web, basé sur l'image nginx.

Exercice 3 — Publier un port
Ajoutez au service web la configuration nécessaire pour publier le port 8080 de l'hôte vers le port 80 du conteneur.

Exercice 4 — Ajouter un deuxième service
Ajoutez un service nommé db, basé sur l'image mysql, avec la variable d'environnement MYSQL_ROOT_PASSWORD définie à secret123.

Exercice 5 — Ajouter un volume nommé
Déclarez un volume nommé db_data, et montez-le sur /var/lib/mysql dans le service db.

Exercice 6 — Dépendance entre services
Indiquez dans le fichier que le service web doit démarrer après le service db.

Exercice 7 — Lancer la stack
Écrivez la commande pour démarrer tous les services définis dans docker-compose.yml, en mode détaché.

Exercice 8 — Voir les logs de la stack
Écrivez la commande pour afficher les logs de tous les services de la stack en même temps.

Exercice 9 — Lister les services actifs
Écrivez la commande docker-compose pour afficher l'état des services (équivalent d'un docker ps mais limité à cette stack).

Exercice 10 — Arrêter et tout supprimer
Écrivez la commande pour arrêter et supprimer tous les conteneurs, réseaux (et éventuellement volumes) créés par ce docker-compose.yml.