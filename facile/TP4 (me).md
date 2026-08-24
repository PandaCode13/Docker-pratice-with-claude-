exercice 1 : 

cmd : docker volume create mes_donnees

exercice 2 : 

cmd : docker volumes 

exercice 3 : 

cmd : docker run -v mes_donnees=/var/lib/mysql mysql

exercice 4 : 

cmd : docker inspect -v mes_donnees 

exercice 5 : 

cmd : docker rmv mes_donnees 

exercice 6 : 

cmd : docker run -d nginx -v /usr/share/nginx/html:/home/user/site 

exercice 7 : 

cmd : docker network create mon_reseau

exerciceN 8 : 

cmd : 

docker network ls 

exercice 9 : 

cmd : docker run -d -n mon_reseau nginx 

exercice 10 : 

cmd : docker rmn mon_reseau