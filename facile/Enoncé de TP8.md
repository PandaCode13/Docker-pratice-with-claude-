Exercice 1 — Utiliser une image plus légère
Réécrivez FROM node:18 pour utiliser à la place la variante alpine de Node 18 (image beaucoup plus légère).

Exercice 2 — Fichier .dockerignore
Créez un fichier .dockerignore qui exclut le dossier node_modules et le fichier .env du contexte de build.

Exercice 3 — Copier uniquement le nécessaire pour le cache
Dans un Dockerfile Node.js, réorganisez les instructions pour copier d'abord package.json (et package-lock.json), faire le npm install, puis copier le reste du code ensuite — plutôt que de tout copier en une seule fois avant l'installation.

Exercice 4 — Pourquoi cet ordre ?
Expliquez en une phrase pourquoi l'ordre de l'exercice 3 permet d'accélérer les reconstructions (docker build) suivantes.

Exercice 5 — Multi-stage build (étape 1)
Écrivez la première étape d'un Dockerfile multi-stage nommée builder, basée sur node:18, qui installe les dépendances et construit l'application avec npm run build.

Exercice 6 — Multi-stage build (étape 2)
Écrivez la seconde étape du même Dockerfile, basée sur nginx:alpine, qui copie uniquement le dossier /app/dist généré par l'étape builder vers /usr/share/nginx/html.

Exercice 7 — Pourquoi le multi-stage build ?
Expliquez en une phrase l'avantage principal d'un build multi-stage par rapport à un Dockerfile classique en une seule étape.

Exercice 8 — Utilisateur non-root
Ajoutez à un Dockerfile l'instruction qui fait en sorte que le conteneur s'exécute avec un utilisateur node (non-root) plutôt qu'en root par défaut.

Exercice 9 — Vérifier la taille d'une image
Écrivez la commande pour afficher la taille de l'image mon-app:1.0 (et comparer, par exemple, une version alpine vs une version classique).

Exercice 10 — Nettoyer les couches inutiles
Dans une instruction RUN qui installe des paquets avec apt-get, ajoutez ce qu'il faut pour supprimer le cache apt dans la même instruction RUN (et non dans une instruction séparée), afin de ne pas alourdir l'image.