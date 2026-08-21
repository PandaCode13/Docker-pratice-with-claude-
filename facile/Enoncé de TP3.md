Exercice 1 — Voir les détails d'un conteneur
Écrivez la commande pour afficher toutes les informations détaillées (au format JSON) du conteneur mon_site.

Exercice 2 — Redémarrer un conteneur
Le conteneur mon_site tourne mais vous voulez le redémarrer (stop + start en une seule commande). Écrivez cette commande.

Exercice 3 — Mettre en pause
Écrivez la commande pour mettre en pause temporairement le conteneur mon_site (les processus sont gelés, sans arrêter le conteneur).

Exercice 4 — Reprendre après pause
Écrivez la commande pour reprendre l'exécution du conteneur mon_site après une pause.

Exercice 5 — Télécharger une image sans la lancer
Écrivez la commande pour télécharger (uniquement) l'image redis depuis Docker Hub, sans créer ni lancer de conteneur.

Exercice 6 — Copier un fichier vers un conteneur
Vous avez un fichier index.html sur votre machine, dans le dossier courant. Écrivez la commande pour le copier dans le conteneur mon_site, à l'emplacement /usr/share/nginx/html/index.html.

Exercice 7 — Copier un fichier depuis un conteneur
À l'inverse, écrivez la commande pour copier le fichier /etc/nginx/nginx.conf du conteneur mon_site vers votre machine locale, dans le dossier courant.

Exercice 8 — Variable d'environnement
Écrivez une commande docker run qui lance un conteneur à partir de l'image mysql, en mode détaché, en définissant la variable d'environnement MYSQL_ROOT_PASSWORD avec la valeur secret123.

Exercice 9 — Limiter la mémoire
Écrivez une commande docker run qui lance un conteneur à partir de l'image nginx, en mode détaché, en limitant sa consommation mémoire à 256 Mo.

Exercice 10 — Nettoyer le système
Écrivez la commande qui supprime automatiquement tous les conteneurs arrêtés, réseaux non utilisés, images non taguées et caches de build inutilisés (nettoyage global du système Docker).