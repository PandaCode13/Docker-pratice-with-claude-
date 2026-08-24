Exercice 1 — Image de base
Dans un Dockerfile, écrivez l'instruction pour indiquer que l'image doit être construite à partir de node:18.

Exercice 2 — Répertoire de travail
Ajoutez l'instruction qui définit /app comme répertoire de travail à l'intérieur du conteneur (toutes les commandes suivantes s'exécuteront depuis ce dossier).

Exercice 3 — Copier des fichiers
Écrivez l'instruction pour copier tout le contenu du dossier courant (sur votre machine) vers le répertoire de travail du conteneur.

Exercice 4 — Exécuter une commande à la construction
Écrivez l'instruction pour exécuter npm install pendant la construction de l'image (pas au démarrage du conteneur).

Exercice 5 — Exposer un port
Écrivez l'instruction indiquant que le conteneur écoutera sur le port 3000.

Exercice 6 — Commande de démarrage
Écrivez l'instruction qui définit la commande à exécuter au démarrage du conteneur : node app.js.

Exercice 7 — Construire une image
Vous avez un Dockerfile dans le dossier courant. Écrivez la commande pour construire une image à partir de ce fichier, en la nommant mon-app.

Exercice 8 — Construire avec un tag
Modifiez la commande précédente pour que l'image soit taguée mon-app:1.0 au lieu de mon-app:latest (tag par défaut).

Exercice 9 — Lancer un conteneur depuis votre image
Écrivez la commande pour lancer un conteneur en mode détaché à partir de l'image mon-app:1.0, en publiant le port 3000 de l'hôte vers le port 3000 du conteneur.

Exercice 10 — Variable d'environnement dans le Dockerfile
Dans un Dockerfile, écrivez l'instruction pour définir une variable d'environnement NODE_ENV avec la valeur production.