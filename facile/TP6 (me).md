exercice 1 à 6: 

dans le fichier docker-compose.yml 

version : 3.8
services :
    web : 
        - image : nginx 
        port : 8080:80
        depends_on: 
            db

    db :
        image : mysql
        environment : 
            - MYSQL_ROOT_PASSWORD = secret123
        volume :
            - "/var/lib/mysql "

volumes : 
    db_data 

exercice 7 

docker run -d docker-compose.yml 

exercice 8 : 

docker logs -a

exercice 9 : 

