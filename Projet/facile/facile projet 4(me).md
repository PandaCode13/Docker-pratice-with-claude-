Partie 1 : 

Question 1 : 

Dans le dossier /todo-app

Dans le fichier .env 
DB_PASSWORD=secret123
APP_PORT=5000

Dans le fichier .dockerignore 
node_modules
.env

Partie 2 : 

Question 2 :

Dans le fichier Dockerfile 

<!-- Stage 1 -->
FROM node:18 AS builder
COPY package*.json ./
RUN npm install 
COPY . .

<!-- Stage 2 -->
FROM node:18-alpine
COPY builder
ENTRYPOINT ["node"]
CMD ["server.js"]

Question 3 : 

Partie 3 : 

Question 4 : 

Dans le fichier docker-compose.yml

services : 
    app:
        port: ${APP_PORT}
        depends_on:
            - db
            condition:
                healthcheck:
    db:
        environments:
            - MYSQL_ROOT_PASSWORD=${MYSQL_ROOT_PASWWORD}
        volumes:
            -todo_db_data
        condition: unless-stopped
        healthcheck:
            CMD ["mysqladmin", "ping"]
            interval: 10s

network: 
    -todo_network

Partie 4 : 

Question 6 : 

docker compose up -d 

Question 7 : 

docker compose logs app
docker compose logs db

Question 8 : 

docker logs 

Question 9 : 

curl http://localhost:5000

Question 10 : 

Partie 5 : 

Question 11 : 

docker exec -it app bash 

Question 12 : 

docker restart app

Question 13 : 

docker tag app monuser/todo-app:1.0

Question 14 : 

docker login 
docker push app 

Partie 6 : 

Question 15 : 

docker rm -f app 
docker rm -f db

Question 16 : 

Question 17 : 