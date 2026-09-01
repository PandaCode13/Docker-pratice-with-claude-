Partie 1 : 

Question 1 : 

docker network create blog_network

Question 2 : 

docker volume create blog_db_data

Question 3 : 

docker volume create blog_files 

Partie 2 : 

Question 4 : 

docker run -d \
--name blog_data \
--network blog_network \
-v blog_db_data:/var/lib/mysql \
-e MYSQL_ROOT_PASSWORD=rootpass \
-e MYSQL_DATABASE=blog \
-e MYSQL_USER=bloguser \
-e MYSQL_PASSWORD=blogpass \
mysql

Question 5 : 

docker ps 

Question 6 : 

docker logs blog_db

Partie 3 : 

Question 7 : 

dans le fichier index.php

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Document</title>
</head>
<body>
    <h1>Bienvenue sur mon blog Docker<h1>
</body>
</html>

Dans le fichier Dockerfile 

FROM php:8.1-cli
WORKDIR /app
COPY . .
EXPOSE 8000
CMD ["php", "-S", "0.0.0.0:8000"]

Question 8 : 

docker image bog-php:1.0

Partie 4 : 

Question 9 : 

Docker run --name blog_nginx -n blog_network --port 8080:80 nginx

Question 10 : 

curl http://localhost:8000

Partie 5 : 

Question 11 : 

docker run --name blog_php -n blog_network --port 8000 blog-php:1.0

Question 12 : 

curl http:localhost:8000

Partie 6 : 

Question 13 : 

docker ps 

Question 14 :

docker ping --name blog_php blog_db

Question 15 : 

docker copy --name blog_php index.php --path /sauvegarde

Question 16 : 

docker inspect blog_db

Partie 7 : 

Question 17 : 

docker stop -a 

Question 18 : 

docker volume rm blog_network

Question 19 :

docker volume rm blog_files

Question 20 : 

Je pense que non 