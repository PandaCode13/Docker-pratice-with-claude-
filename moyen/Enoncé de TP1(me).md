Exercice 1 : 

docker run ubuntu
curl 

docker image create ubuntu-curl:1.0 from ubuntu 

Exercice 2 : 

docker diff ubuntu 

Exercice 3 : 

docker export ubuntu-curl:1.0 ubuntu-curl.tar

Exercice 4 : 

docker load -i ubuntu-curl.tar

Exercice 5 : 

docker run -d --cpus="0.5" nginx

Exercice 6 : 

docker 

Exercice 7 : 

docker run -d 

Exercice 8 : 

Dans le fichier dockerfile

from node:18
CMD ["curl", "-f", "http://localhost"]
INTERVAL 15s
CONDITION 3s unhealthy

Exercice 9 : 

Aucune idée 

Exercice 10 : 

docker network connect shop_network shop_api

Exercice 11 :

docker network inspect shop_network

Exercice 12 :

Exercice 13 : 

Exercice 14 : 

docker logs shop_api --last-history 50

Exercice 15 :