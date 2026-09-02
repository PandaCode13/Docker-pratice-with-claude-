Partie 1 : 

Question 1 : 

docker network create api_network 

Question 2 : 

docker volume create redis_data

Partie 2 : 

Question 3 : 

docker run -d api_redis --network api_network --volume redis_data:/data 

Question 4 : 

docker ps 

Question 5 : 

docker logs api_redis

Partie 3 : 

Question 6 : 

package.json 

{
    "name" : "api", 
    "version" : "1.0.0"
    "main" : "server.js", 
    "dependancies" : {
        "name" : "redis", 
        "version" : "3.12"
    }
}

Dockerfile

FROM node:18
WORKDIR /app
COPY package.json package-lock.json ./
RUN "npm install"
COPY . .
EXPOSE 4000
CMD ["node", "server.js"]

Partie 4 : 

Question 8 : 

docker run --name api-app -n api_network -p 4000 -e REDIS_HOST=${REDIS_HOST}

Question 9 : 

curl http://localhost:4000

Partie 5 : 

Question 10: 

docker ps -a

Question 11 :

docker ping --name api_redis

Question 12 : 

docker copy server.js api_app /sauvegarde_api

Question 13 : 

docker inspect api_redis

Question 14 : 

docker 

Partie 6 : 

Question 15 : 

docker rm -f api_redis 
docker rm -f api_app

Question 16 : 

docker network rm api_network

Question 17 : 

Question 18 : 