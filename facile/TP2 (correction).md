Exercice 1 : 

cmd : docker logs mon_site

correction : docker logs mon_site 

Exercice 2 : 

cmd : docker logs -a mon_site

correction : docker logs -f mon_site

Exercice 3 : 

cmd : docker 

correction : docker exec -it mon_site bash 

Exercice 4 : 

cmd : docker ps -a 

correction : docker images // docker images ls 

Exercice 5 : 

cmd : docker rm nginx 

correction : docker rmi nginx 

explication : 

rm est la commande pour supprimer les conteneurs tandis que la commande rmi est la commande pour supprimer spécifiquement des images. 