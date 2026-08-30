Partie 1 : 

Exercice 1 : 

````bash
    FROM alpine 
    ENTRYPOINT ["echo", "Hello"]
````

Exercice 2 : 

````bash 
    FROM alpine 
    ENTRYPOINT ["echo", "Hello"]
    CMD ["Docker"]
````

Exercice 3 : 

````bash
    docker run mon-image Monde
````

Exercice 4 : 

Partie 2 : 

Exercice 5 :

ARG NODE_VERSION = 18
FROM node:${NODE_VERSION}

Exercice 6 :

docker build --build-arg NODE_VERSION=20 -t mon-app:1.0

Exercice 7

En une phrase, expliquez la différence principale entre ARG et ENV (disponibilité, persistance dans l'image finale).

ARG n'existe que pendant la construction de l'image (accessible uniquement dans le Dockerfile), tandis que ENV définit une variable qui persiste dans l'image finale et reste donc disponible dans tous les conteneurs lancés à partir de cette image.

Exercice 8

Peut-on accéder à une variable définie avec ARG une fois le conteneur démarré (via docker exec ... env) ? Justifiez votre réponse

Non. Une fois le conteneur démarré, docker exec web env n'affichera pas les variables ARG — elles ont disparu après le build, sauf si on les a explicitement recopiées dans une variable ENV dans le Dockerfile (ex : ENV NODE_VERSION=${NODE_VERSION}).

Partie 3 : 

Exercice 9 : 

````bash 
    docker tag mon-app:1.0 monuser/mon-app:1.0 
````

Mini explication : docker tag ne duplique pas l'image sur le disque, elle crée juste un alias pointant vers les mêmes couches. Le format utilisateur/nom_image:tag est obligatoire pour publier sur Docker Hub sous votre compte.

Exercice 10 : 

docker login

Exercice 11 : 

docker push monuser/mon-app:1.0

Exercice 12 : 

docker pull monuser/mon-app:1.0

Exercice 13 : 

docker images monuser/*

Partie 4 : 

Exercice 14 : 

docker stats 

Exercice 15 : 

docker stats --no-stream web 

Exercice 16 : 

docker top web

Partie 5 : 

Exercice 17 : 

docker compose up -d 

Exercice 18 : 

docker compose -f docker-compose.yml -f docker-compose.prod.yml up -d

Partie 6 — Précédence des variables d'environnement

Exercice 19

C'est PORT=4000 (celui défini directement dans environment: du docker-compose.yml) qui l'emporte. Le fichier .env sert avant tout à substituer des ${VARIABLE} dans le fichier YAML lui-même (par exemple pour l'image ou les ports), mais si une variable est explicitement redéfinie dans environment:, cette valeur explicite est prioritaire sur celle qui aurait pu venir du .env.

Exercice 20

C'est PORT=5000 qui sera utilisé dans le conteneur.

Classement du plus prioritaire au moins prioritaire :

-e de docker run (ou environment: en Compose) — la plus prioritaire, définie au lancement du conteneur.
.env de Compose — seulement utilisé pour la substitution dans le fichier YAML, n'intervient pas s'il y a un conflit direct avec environment:.
ENV du Dockerfile — la moins prioritaire, c'est juste la valeur par défaut intégrée dans l'image, écrasée dès qu'une variable du même nom est fournie au moment du run.