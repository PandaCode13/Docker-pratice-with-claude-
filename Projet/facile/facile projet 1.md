🎯 Projet : Mini stack web avec base de données

Contexte : tu vas construire et faire tourner une petite architecture composée de deux conteneurs qui communiquent entre eux : une application web (que tu vas "dockeriser" toi-même) et une base de données MySQL, reliées par un réseau Docker, avec persistance des données.

📋 Cahier des charges

Partie 1 — Préparation

Créez un réseau Docker nommé app_network.
Créez un volume nommé db_data pour stocker les données de la base de données de façon persistante.

Partie 2 — Base de données
3. Lancez un conteneur MySQL nommé db, connecté au réseau app_network, avec le volume db_data monté sur /var/lib/mysql, et la variable d'environnement MYSQL_ROOT_PASSWORD définie à secret123.
4. Vérifiez que le conteneur tourne bien.
5. Consultez les logs de db pour vérifier qu'il n'y a pas d'erreur au démarrage.

Partie 3 — Application web (Dockerfile)
6. Créez un dossier de projet contenant :

Un fichier app.js très simple (un serveur Node.js minimal, par exemple avec le module http natif, qui répond "Hello Docker" sur le port 3000).
Un Dockerfile qui :
part de l'image node:18
définit /app comme répertoire de travail
copie les fichiers du projet
expose le port 3000
lance node app.js au démarrage
Construisez l'image à partir de ce Dockerfile, en la nommant mon-app:1.0.

Partie 4 — Lancement de l'application
8. Lancez un conteneur nommé web à partir de l'image mon-app:1.0, connecté au réseau app_network, avec le port 3000 publié vers l'hôte.
9. Vérifiez dans votre navigateur (ou avec curl) que http://localhost:3000 répond bien.

Partie 5 — Vérifications & manipulations
10. Depuis le conteneur web, essayez de "pinguer" le conteneur db par son nom (via docker exec) pour prouver qu'ils communiquent bien sur le même réseau.
11. Copiez le fichier app.js du conteneur web vers votre machine locale (dans un dossier backup/), avec docker cp.
12. Affichez les informations détaillées (inspect) du conteneur web et retrouvez l'adresse IP interne qui lui a été attribuée sur app_network.

Partie 6 — Nettoyage
13. Arrêtez et supprimez les deux conteneurs (web et db) en une seule commande chacun.
14. Supprimez le réseau app_network.
15. Le volume db_data doit-il être supprimé aussi ? Justifiez votre réponse (indice : réfléchissez à ce que représente ce volume).