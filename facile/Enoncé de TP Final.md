🎯 TP Final — Docker de A à Z

Contexte : vous allez construire, configurer, déboguer, optimiser et publier une stack e-commerce simplifiée (API + base de données), en mobilisant toutes les commandes et concepts vus depuis le début.

Partie 1 — Bases : lancer, nommer, publier (TP1)

1. Lancez un conteneur nginx en mode détaché, sans nom particulier.
2. Relancez-le cette fois nommé shop_web, avec le port 8080 publié vers le port 80 du conteneur.
3. Listez les conteneurs en cours d'exécution.
4. Arrêtez puis supprimez shop_web.

Partie 2 — Logs, exec, images (TP2)

5. Lancez un conteneur mysql nommé shop_db, avec MYSQL_ROOT_PASSWORD=secret123, en mode détaché.
6. Affichez ses logs, puis suivez-les en direct.
7. Ouvrez un terminal bash à l'intérieur de shop_db.
8. Listez toutes les images présentes localement.

Partie 3 — Cycle de vie, ressources, transfert (TP3)

9. Affichez les informations détaillées (JSON) de shop_db avec inspect.
10. Redémarrez shop_db en une seule commande.
11. Mettez shop_db en pause, puis reprenez son exécution.
12. Copiez un fichier dump.sql de votre machine vers /tmp/dump.sql dans shop_db.
13. Relancez shop_db avec une limite mémoire de 512m.

Partie 4 — Volumes et réseaux (TP4)

14. Créez un volume shop_db_data et un réseau shop_network.
15. Relancez shop_db sur shop_network, avec shop_db_data monté sur /var/lib/mysql.
16. Inspectez le volume shop_db_data pour retrouver son emplacement réel sur le disque.

Partie 5 — Dockerfile de base (TP5)

17. Créez un Dockerfile pour une API Node.js (node:18, WORKDIR /app, copie du code, EXPOSE 4000, démarrage avec node server.js).
18. Construisez cette image sous le nom shop-api:1.0.

Partie 6 — Bonnes pratiques Dockerfile (TP8)

19. Réécrivez ce Dockerfile en multi-stage build : une étape builder (node:18) qui installe les dépendances et copie le code, puis une étape finale (node:18-alpine) qui ne récupère que le nécessaire.
20. Ajoutez un .dockerignore excluant node_modules et .env.
21. Faites en sorte que le conteneur final s'exécute avec un utilisateur non-root.
22. Ajoutez un ARG NODE_ENV=production, transformé ensuite en ENV persistant dans l'image.

Partie 7 — Docker Compose (TP6 + TP7)

23. Écrivez un docker-compose.yml avec deux services : db (MySQL, volume, restart: unless-stopped, healthcheck via mysqladmin ping) et api (construit depuis votre Dockerfile multi-stage, port publié, depends_on: db avec condition: service_healthy).
24. Ajoutez les deux services au réseau shop_network déclaré dans la section networks:.
25. Créez un fichier .env définissant DB_PASSWORD et API_PORT, et utilisez ces variables dans le docker-compose.yml.
26. Lancez toute la stack en une seule commande, en arrière-plan.

Partie 8 — Exploitation de la stack (TP7 + TP9)

27. Affichez l'état des services de la stack (docker compose ...).
28. Affichez les logs de tous les services en une seule commande.
29. Ouvrez un terminal dans le service api via docker compose exec.
30. Redémarrez uniquement le service api, sans toucher à db.
31. Affichez en temps réel la consommation CPU/mémoire de tous les conteneurs de la stack.
32. Lancez 3 instances du service api en parallèle avec --scale.

Partie 9 — Registre Docker Hub (TP9)

33. Taguez l'image construite pour api sous le nom monuser/shop-api:1.0.
34. Connectez-vous à Docker Hub.
35. Publiez cette image sur Docker Hub.
36. Depuis une machine "vide", récupérez cette image avec docker pull.

Partie 10 — Nettoyage et réflexion finale

37. Arrêtez et supprimez tous les conteneurs et le réseau de la stack, en conservant les volumes.
38. Cette fois, supprimez tout y compris les volumes.
39. Supprimez le réseau shop_network et le volume shop_db_data créés manuellement en Partie 4 (hors Compose).
40. Le fichier .env doit-il être versionné dans Git ? Et le Dockerfile ? Justifiez les deux réponses séparément.