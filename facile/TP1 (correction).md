exercice 1 : 

enoncé : 
Lancer un conteneur simple
Écrivez la commande pour lancer un conteneur à partir de l'image nginx, sans lui donner de nom particulier, en mode détaché.

ma réponse : docker run --name nginx -d 
correction: docker run -d nginx

Exercice 2 : 

Enoncé : 

Nommer un conteneur
Modifiez la commande précédente pour que le conteneur s'appelle mon_site.

ma réponse : docker rename nginx mon_site
correction : docker run --name mon_site -d nginx 

Exercice 3 :

Enoncé : 

Publier un port
En partant de l'exercice 2, ajoutez l'option nécessaire pour rendre le serveur Nginx accessible depuis votre navigateur, sur le port 8080 de votre machine (qui doit correspondre au port 80 du conteneur).

ma réponse : docker rename nginx mon_site --port 8080
correction : docker run --name mon_site -d -p 8080:80 nginx 

Exercice 4 : 

Lister les conteneurs
Une fois votre conteneur mon_site lancé, quelle commande utilisez-vous pour voir la liste des conteneurs actuellement en cours d'exécution ?

ma réponse : docker ps -a 
correction : docker ps 

Exercice 5 :

Arrêter et supprimer
Écrivez les deux commandes nécessaires pour d'abord arrêter le conteneur mon_site, puis le supprimer complètement.

ma réponse : 

docker stop mon_site 
docker rm mon_site

correction : tout est correct 

Astuce :  docker rm -f mon_site permet de forcer l'arret et la suppression en une seule commande 