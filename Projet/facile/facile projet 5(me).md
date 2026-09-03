Partie 1 : 

Question 1 : 

docker run -d\
--name notes_db\
-e MYSQL_ROOT_PASSWORD=secret123\
mysql

Question 2 : 

docker ps 
docker logs notes_db

Question 3 : 

docker rm -f notes_db

Partie 2 : 

Question 4 : 

je ne sais pour le fichier server.js 

.dockerignore 
node_modules

Question 5 : 

Dans le fichier Dockerfile

FROM node:18 AS builder
COPY package*.json ./
RUN npm install
COPY . .

FROM node:18-alpine
COPY builder
CMD ["node", "server.js"]

Question 6 : 

FROM node:18-alpine
ARG NODE_ENV=production
COPY builder
CMD ["node", "server.js"]

Partie 3 : 

Question 7 : 

dans le fichier .env 
DB_PASSWORD=secret123 
API_PORT=4000

Question 8 :

Dans le fichier docker-compose.yml

services:
    api:
        port=${API_PORT}
        depends_on:
            -db
    
    db:
        environments:
            -MYSQL_ROOT_PASSWORD=${MYSQL_ROOT_PASSWORD}
        restart: unless-stopped
        volumes:
            -notes_db_data
        healthcheck:
            CMD ["mysqladmin", "ping"]
            interval: 10s

Question 9 : 

api: 
    network:
        -notes_network

db: 
    network:
        -notes_network

Partie 4 : 

Question 10 : 

docker compose up -d 

Question 11 : 

docker logs -f api 
docker logs -f db

Question 12 : 

curl http://localhost:4000

Question 13 : 

Partie 5 : 

Question 14 : 

docker exect -it api bash 

Question 15 : 

Question 16 : 

docker restart api 

Partie 6 : 

Question 17 : 

docker tag api monuser/notes-api:1.0

Question 18 : 

docker login 
docker pull 

Partie 7 : 

Question 19 : 

Question 20 : 

