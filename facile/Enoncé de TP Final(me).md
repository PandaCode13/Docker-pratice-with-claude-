Partie 1 : 

Question 1 : 

docker run nginx

Question 2 : 

docker run shop_web -p 8080:80

Question 3 : 

docker ps 

Question 4 : 

docker rm -f shop_web

Partie 2 : 

Question 5 : 

docker run -d\
--name shop_db\
-e MYSQL_ROOT_PASSWORD=secret123\
mysql 

Question 6 : 

docker logs shop_db

Question 7 : 

docker exec -it shop_db bash 

Question 8 : 

docker ps 

Partie 3 : 

Question 9 : 

docker inspect shop_db 

Question 10 : 

docker restart shop_db

Question 11 : 

docker pause shop_db 
docker unpause shop_db

Question 12 : 

docker copy dump.sql shop_db:/tmp/dump.sql

Question 13 : 

docker run -d --memory=512m shop_db

Question 14 : 

docker volume create shop_db_data
docker network create shop_network

Question 15 : 

docker restart shop_db -n shop_network -v shop_db_data:/var/lib/mysql

Question 16 : 

docker inspect -v shop_db_data

Partie 5 : 

Question 17 : Dockerfile 

FROM node:18
WORKDIR /app
COPY package*.json ./
COPY . . 
EXPOSE 4000
CMD ["node", "server.js"]

Question 18 : 

Partie 6 : 

Question 19 : 

FROM node:18 AS builder
WORKDIR /app
COPY package*.json ./
COPY . . 
EXPOSE 4000
CMD ["node", "server.js"]

Question 20 : 

.dockerignore 

node_modules 
.env

Question 21 : 

Question 22 : 

Partie 7 : 

Question 23 && 24 : 

docker-compose.yml 

services : 
    api: 
        build:
        network:
            -shop_network
        depends_on: 
            - db
            condition: service_healthy
    
    db:
        image: MySQL
        network:
            -shop_network
        volume:
        restart: unless-stopped
        healthcheck: 
            cmd: "mysqladmin ping"

network:
    shop_network

Question 25 : 

Dans le fichier .env 
DB_PASSWORD=
API_PORT=

Question 26 : 

docker compose up -d

Partie 8 : 

Question 27 :

docker compose 

Question 28 : 

docker logs 

Question 29 : 

docker compose exec -it api

Question 30 : 

docker restart api

Question 31 : 

docker 

Question 32 : 

docker run --scale 3 api 

Partie 9 : 

Question 33 : 

docker tag api monuser/shop-api:1.0

Question 34 : 

docker login 

Question 35 : 

Question 36 : 

