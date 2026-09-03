🎯 Projet 5 : Plateforme de notes (Notes API) — stack complète de A à Z

Contexte : tu vas construire, de bout en bout, une petite application de prise de notes (API Node.js + MySQL), en passant par toutes les étapes d'un vrai projet Docker : conteneurs isolés, Dockerfile optimisé, orchestration avec Compose, et publication de l'image.

📋 Cahier des charges

Partie 1 — Découverte manuelle (sans Compose)

Lancez un conteneur mysql nommé naotes_db, en mode détaché, avec MYSQL_ROOT_PASSWORD=secret123.
Vérifiez qu'il tourne bien, et consultez ses logs pour confirmer que MySQL est prêt.
Arrêtez et supprimez ce conteneur — on va tout refaire proprement avec Compose ensuite.

Partie 2 — Dockerfile de l'API
4. Créez un dossier notes-api/ avec :

Un fichier server.js (serveur Node.js minimal sur le port 4000, qui répond "API Notes en ligne" sur /).
Un .dockerignore excluant node_modules.
Écrivez un Dockerfile multi-stage :
Étape builder (node:18) : copie package.json, installe les dépendances, copie le reste du code.
Étape finale (node:18-alpine) : récupère uniquement le nécessaire depuis builder, s'exécute avec un utilisateur non-root, et démarre avec CMD ["node", "server.js"].
Ajoutez un ARG NODE_ENV=production transformé en ENV persistant dans l'image finale.

Partie 3 — Docker Compose
7. Créez un fichier .env définissant DB_PASSWORD=secret123 et API_PORT=4000.
8. Écrivez un docker-compose.yml avec :

un service db (MySQL) : volume nommé notes_db_data, MYSQL_ROOT_PASSWORD venant du .env, restart: unless-stopped, et un healthcheck (mysqladmin ping toutes les 10s).
un service api : construit depuis le Dockerfile de la Partie 2, port publié via la variable API_PORT, et démarrage conditionné à db healthy.
Déclarez un réseau notes_network utilisé par les deux services.

Partie 4 — Lancement et vérifications
10. Lancez toute la stack en une seule commande, en arrière-plan.
11. Affichez l'état des services, puis leurs logs réunis en une seule commande.
12. Vérifiez avec curl que l'API répond bien sur le port défini dans .env.
13. Affichez en temps réel la consommation CPU/mémoire des conteneurs de la stack.

Partie 5 — Debug courant
14. Ouvrez un terminal bash à l'intérieur du service api (via docker compose exec).
15. Depuis ce terminal, vérifiez que db est bien joignable par son nom.
16. Redémarrez uniquement le service api, sans toucher à db.

Partie 6 — Publication
17. Taguez l'image construite pour api sous le nom monuser/notes-api:1.0.
18. Connectez-vous à Docker Hub, puis publiez cette image.

Partie 7 — Nettoyage et bilan
19. Arrêtez et supprimez conteneurs + réseau, en conservant le volume notes_db_data.
20. Le fichier .env doit-il être versionné dans Git ? Et le Dockerfile ? Justifiez les deux séparément.