Exercice 1 — Construire depuis un Dockerfile
Au lieu d'utiliser une image existante, modifiez un service web pour qu'il soit construit à partir d'un Dockerfile situé dans le dossier courant (au lieu de image: nginx).

Exercice 2 — Nommer l'image construite
En gardant le build de l'exercice précédent, ajoutez une option pour que l'image construite soit quand même taguée mon-app:1.0.

Exercice 3 — Fichier .env
Vous avez un fichier .env contenant la ligne DB_PASSWORD=secret123. Dans le docker-compose.yml, utilisez cette variable pour définir MYSQL_ROOT_PASSWORD dans le service db (sans écrire la valeur en dur).

Exercice 4 — Politique de redémarrage
Ajoutez au service db une politique de redémarrage automatique en cas de plantage, sauf si le conteneur a été arrêté manuellement.

Exercice 5 — Healthcheck
Ajoutez un healthcheck au service db qui vérifie toutes les 10s que MySQL répond, en utilisant la commande mysqladmin ping.

Exercice 6 — Attendre qu'un service soit "healthy"
Modifiez la dépendance du service web envers db (vue au TP6) pour qu'il attende non seulement que db soit démarré, mais qu'il soit réellement prêt (healthy), grâce au healthcheck de l'exercice précédent.

Exercice 7 — Réseau personnalisé
Déclarez un réseau nommé mon_reseau dans la section networks:, puis connectez-y les deux services web et db.

Exercice 8 — Exécuter une commande dans un service
Écrivez la commande docker compose équivalente à docker exec -it pour ouvrir un terminal bash à l'intérieur du service web (sans connaître le nom exact du conteneur généré).

Exercice 9 — Redémarrer un seul service
Écrivez la commande pour redémarrer uniquement le service web, sans toucher aux autres services de la stack.

Exercice 10 — Mettre à l'échelle un service
Écrivez la commande pour lancer 3 instances du service web en parallèle (sans modifier le fichier docker-compose.yml).