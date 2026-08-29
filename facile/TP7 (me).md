exercice 1 à 7:

services : 
    web : 
        build :
        image : mon-app:1.0

    db :
        environments :
            - MYSQL_ROOT_PASSWORD = ${DB_PASSWORD}
        healthcheck : 

networks : 
    - web 
    - db

exercice 8 : 

docker compose up -d --name web 

exercice 9 : 

docker restart 

exercice 10 : 

aucune idée 
