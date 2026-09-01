Partie 1 : 

Question 1 : 

docker network create blog_network

correction : c'est juste 

Question 2 : 

docker volume create blog_db_data

correction : c'est juste 

Question 3 : 

docker volume create blog_files 

correction : c'est juste

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

correction : c'est faux sur le nom. Voici la correction 

``` bash

docker run -d \
  --name blog_db \
  --network blog_network \
  -v blog_db_data:/var/lib/mysql \
  -e MYSQL_ROOT_PASSWORD=rootpass \
  -e MYSQL_DATABASE=blog \
  -e MYSQL_USER=bloguser \
  -e MYSQL_PASSWORD=blogpass \
  mysql

```

Question 5 : 

docker ps 

correction : c'est juste 

Question 6 : 

docker logs blog_db

correction : c'est juste 

Partie 3 : 

Question 7 : 

dans le fichier index.php

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Document</title>
</head>
<body>
    <h1>Bienvenue sur mon blog Docker</h1>
</body>
</html>

Dans le fichier Dockerfile 

FROM php:8.1-cli
WORKDIR /app
COPY . .
EXPOSE 8000
CMD ["php", "-S", "0.0.0.0:8000"]

correction : c'est juste pour le fichier index.php et le dockerfile

Question 8 : 

docker image bog-php:1.0

correction : c'est faux 

``` bash
    docker build -t blog-php:1.0
```

Partie 4 : 

Question 9 : 

Docker run --name blog_nginx -n blog_network --port 8080:80 nginx

correction : c'est faux pour cette partie
```bash 
    -n blog_network 
```

``` bash
    docker run -d \
    --name blog_nginx\
    --network blog_network\
    -p 8080:80 ( ou --port 8080:80)\
    nginx
```

Question 10 : 

curl http://localhost:8000

correction : c'est juste 

Partie 5 : 

Question 11 : 

docker run --name blog_php -n blog_network --port 8000 blog-php:1.0

correction : c'est faux pour cette partie
```bash 
    -n blog_network 
```

``` bash
    docker run -d \
    --name blog_php \
    --network blog_network \
    -p 8000:8000 \
    blog-php:1.0
```

Question 12 : 

curl http:localhost:8000

correction : c'est juste 

Partie 6 : 

Question 13 : 

docker ps 

correction : c'est juste 

Question 14 :

docker ping --name blog_php blog_db

correction : c'est juste à moitié, il manque des trucs 

``` bash
    docker exec -it blog_php ping blog_db
```

👉 Si ping n'est pas disponible dans l'image php:8.1-cli :

``` bash
    docker exec -it blog_php getent hosts blog_db
```

Cela retourne l'adresse IP interne de blog_db, ce qui prouve que la résolution DNS fonctionne bien sur blog_network.

Question 15 : 

docker copy --name blog_php index.php --path /sauvegarde

correction : c'est faux 

``` bash
    mkdir -p sauvegarde 
    docker cp blog_php:/app/index.php sauvegarde/
```

Question 16 : 

docker inspect blog_db

correction : c'est juste 

    Si c'est pour cibler l'IP directement : voici la commande 
``` bash
    docker inspect -f '{{.NetworkSettings.Networks.blog_network.IPAddress}}' blog_db
```

Partie 7 : 

Question 17 : 

docker stop -a 

CORRECTION : Completement faux. Rien à voir 

``` bash
    docker rm -f blog_php
    docker rm -f blog_nginx
    docker rm -f blog_db
```

Question 18 : 

docker volume rm blog_network

correction : c'est juste 

Question 19 :

docker volume rm blog_files

correction : c'est juste 

Question 20 : 

Je pense que non 

Correction :
Non, pas forcément. Le volume blog_db_data contient toutes les données de la base MySQL (blog, bloguser, les tables créées...). Ces données existent indépendamment du conteneur blog_db — c'est justement l'intérêt d'un volume.

Si le projet est terminé définitivement et que les données ne servent plus → docker volume rm blog_db_data.
Si on compte relancer le projet plus tard → on conserve le volume. Au prochain docker run avec -v blog_db_data:/var/lib/mysql, MySQL retrouvera toutes ses données exactement comme on les avait laissées.