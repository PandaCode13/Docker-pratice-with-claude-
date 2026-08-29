services : 
    web : 
        build : 
        image : mon_app:1.0
        networks : 
            - mon_reseau
        depends_on : 
            db : 
                condition : service_healthy
    
    db : 
        image : mysql 
        environment : 
            - MYSQL_DB_PASSWORD : ${DB_PASSWORD}
        restart : unless-stopped
        healthcheck : 
            test : ["CMD", "mysqladmin", "ping"]
            interval : 10s
        networks : 
            - mon_reseau

networks : 
    - mon_reseau

docker compose exec web bash

docker compose restart web

docker compose up -d --scale web=3