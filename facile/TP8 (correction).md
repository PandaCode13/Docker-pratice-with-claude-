Exercice 1 : 

FROM node:18-alpine 

Exercice 2 : 

fichier .dockerignore 

node_modules 
.env

Exercice 3 : 

dans le fichier Dockerfile

FROM node:18-alpine
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm install
COPY . .
CMD ["node", "app.js"]

Exerice 4 : 

Docker met en cache chaque instruction du Dockerfile : tant que package.json ne change pas, Docker réutilise le cache de l'étape npm install (qui est souvent la plus longue) même si vous modifiez juste un fichier de code source — alors qu'avec un COPY . . unique placé avant RUN npm install, la moindre modification de code invaliderait le cache et forcerait à tout réinstaller à chaque build.

Exercice 5 : Multi-stage build (étape 1)

FROM node:18 AS builder
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm install
COPY . .
RUN npm run build

Exercice 6 : 

FROM nginx:alpine
COPY --from=builder /app/dist /usr/share/nginx/html

Exercice 7 — Pourquoi le multi-stage build ?
L'avantage principal est de produire une image finale beaucoup plus légère et plus sûre : elle ne contient que le strict nécessaire à l'exécution (ici, juste Nginx + les fichiers statiques), sans tous les outils, dépendances et fichiers intermédiaires utilisés uniquement pendant la phase de build.

Exercice 8 : 

FROM node:18-alpine
WORKDIR /app
COPY . .
RUN npm install
USER node
CMD ["node", "app.js"]

USER node fait en sorte que toutes les instructions suivantes (et le processus final du conteneur) s'exécutent avec les droits de l'utilisateur node plutôt qu'en root. C'est une bonne pratique de sécurité : si un attaquant compromettait l'application, il n'aurait pas les pleins pouvoirs à l'intérieur du conteneur.
👉 Note : l'utilisateur node existe déjà par défaut dans les images officielles Node.js — pour une image sans cet utilisateur prédéfini, il faudrait le créer soi-même avec RUN adduser.

exercice 9 : 

docker images mon-app:1.0

exercice 10 : 

RUN apt-get update && apt-get install -y curl && rm -rf /var/lib/apt/lists/*

Chaque instruction RUN crée une nouvelle couche dans l'image, qui est conservée définitivement même si on supprime des fichiers dans une instruction RUN séparée plus tard. En combinant update, install et le nettoyage du cache (rm -rf /var/lib/apt/lists/*) dans une seule et même instruction (reliée par &&), le nettoyage se fait avant que la couche ne soit "figée", ce qui réduit réellement la taille finale de l'image.