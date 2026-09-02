🎯 Projet 4 : Stack complète avec Docker Compose + publication sur Docker Hub

Contexte : tu vas monter une stack Todo List (API Node.js + MySQL) entièrement pilotée par docker-compose, avec une image construite en multi-stage build, publiée ensuite sur Docker Hub.

📋 Cahier des charges

Partie 1 — Préparation du projet

Créez un dossier todo-app/ contenant :
Un fichier .env définissant DB_PASSWORD=secret123 et APP_PORT=5000.
Un fichier .dockerignore excluant node_modules et .env.

Partie 2 — Dockerfile multi-stage
2. Écrivez un Dockerfile en deux étapes :

Étape builder (basée sur node:18) : installe les dépendances (package.json copié en premier pour profiter du cache) et copie le code source.
Étape finale (basée sur node:18-alpine) : copie uniquement le nécessaire depuis builder, tourne en utilisateur non-root, et utilise ENTRYPOINT ["node"] avec CMD ["server.js"] (pour pouvoir remplacer facilement le fichier lancé si besoin).
Ajoutez un ARG nommé NODE_ENV avec la valeur par défaut production, et transformez-le en variable d'environnement persistante dans l'image.

Partie 3 — docker-compose.yml
4. Écrivez un docker-compose.yml déclarant deux services :

db (MySQL) : avec un volume nommé todo_db_data, la variable MYSQL_ROOT_PASSWORD provenant du .env, une politique de redémarrage unless-stopped, et un healthcheck (mysqladmin ping toutes les 10s).
app : construit depuis le Dockerfile de la Partie 2, publie le port défini par APP_PORT (venant du .env) vers le port 5000 du conteneur, et démarre seulement une fois que db est healthy.
Déclarez un réseau personnalisé todo_network utilisé par les deux services.

Partie 4 — Lancement et vérifications
6. Lancez toute la stack en une seule commande, en arrière-plan.
7. Vérifiez l'état des deux services via la commande docker compose dédiée.
8. Consultez les logs de tous les services en une seule commande.
9. Vérifiez avec curl que l'API répond bien sur le port défini dans .env.
10. Affichez en temps réel la consommation CPU/mémoire des conteneurs de la stack.

Partie 5 — Debug et publication
11. Ouvrez un terminal bash à l'intérieur du service app (via la commande docker compose dédiée, sans connaître le nom exact du conteneur).
12. Redémarrez uniquement le service app, sans toucher à db.
13. Taguez l'image construite pour votre service app en vue d'une publication sous le nom monuser/todo-app:1.0.
14. Connectez-vous à Docker Hub, puis publiez cette image.

Partie 6 — Nettoyage
15. Arrêtez et supprimez tous les conteneurs et le réseau de la stack, en conservant les volumes.
16. Une seconde fois, arrêtez et supprimez tout y compris les volumes cette fois-ci.
17. Le fichier .env doit-il être committé dans Git avec le reste du projet ? Justifiez votre réponse.