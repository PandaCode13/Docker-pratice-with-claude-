Exercice 1 : 

docker run -d \
--name shop_api shop-api:1.0\ 
--port 4000 \
--drive-opt bridge

docker run -d\
--rename shop_api_host shop_api\
--port 4000\
--drive-opt bridge

Exercice 2 : 

docker run -d\
--name shop_worker alpine\
--drive-opt none

Exercice 3 : 

docker create network shop_backend_net \
--ip4 172.28.0.0/16 \
--drive-opt bridge

Exercice 4 : 

docker run -d \ 
--name shop_db \
--network shop_backend_net\
--ip4 172.28.0.10

Exercice 5 : 

docker network connect -d \
--name shop_db \
--network shop_backend_net\
--alias database 

Exercice 6 : 

docker network create shop_frontend_net

docker network connect shop_frontend_net shop_proxy
docker network connect shop_frontend_net shop_frontend

docker network connect shop_frontend_net shop_api
docker network connect shop_backend_net shop_api

docker network connect shop_backend_net shop_db

après cela pour une pure vérification : 

docker inspect shop_proxy
docker inspect shop_frontend
docker inspect shop_api
docker inspect shop_db

docker network inspect shop_frontend_net
docker network inspect shop_backend_net

Exercice 7 : 

docker compose exec -it --name shop_db --port 3306

Exercice 8 : 

docker exec -it bash shop_api

Exercice 9 : 

docker network inspect shop_backend_net 

Exercice 10 : 

docker network disconnect shop_frontend_net shop_api

Exercice 11 : 

docker run -d\
--name shop_debug\
nicolaka/netshoot
--network shop_backend_net

Exercice 12 : 

docker 

Exercice 13 : 

docker network ls 

Exercice 14 : 

Dans le fichier docker-compose.yml 

networks :
    - shop_backend_net
    - shop_frontend_net

Exercice 15 : 

docker 