question 1 : 

docker network create app_network

question 2 : 

docker volume create db_data

question 3 : 

docker run -d --name db --network app_network --volume db_data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=secret123 mysql

question 4 : 

docker ps 

question 5 : 

docker logs db

question 6 : 

dans le fichier app.js 

const http = require("http");

const server = http.createServer((req, res) => {
    res.writeHead(200, {'Content-Type': 'text/plain'});
    res;end("Hello Docker");
});

server.listen(3000, () => {
    console.log("Serveur démarré sur le port 3000")
})

Dans le Dockerfile 

FROM node:18
WORKDIR app
COPY . . 
EXPOSE 3000
RUN "node app.js"

question 7 : 

docker build -t exec mon-app:1.0

question 8 : 

docker run --name web --network app_network --port 3000 

question 9 : 

curl http://localhost:3000

question 10 : 

docker exec -t it 

question 11 : 

mkdir -p backup
docker cp --name web backup

question 12 : 

docker inspect --name web 

question 13 : 

docker rm -f web (equivlaent : docker stop web && docker rm web)
docker rm -f db (equivlaent : docker stop db && docker rm db)

question 14 : 

docker network rm app_network 