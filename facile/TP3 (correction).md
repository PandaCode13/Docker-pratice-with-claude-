Exercice 1 : 

cmd : docker inspect mon_site

correction: tout est correct. Retourne un objet JSON complet avec toutes les métadonnées du conteneur : configuration réseau, volumes montés, variables d'environnement, chemins internes, etc.

Exercice 2 : 

cmd : docker stop start mon_site

correction : c'est faux. La vraie commande est ```bash  docker restart mon_site```. Équivaut à faire docker stop puis docker start à la suite, mais en une seule commande.


Exercice 3 : 

cmd : docker pause mon_site

correction : la commande est correct. 
explication : Gèle tous les processus à l'intérieur du conteneur (via un mécanisme du noyau Linux appelé cgroups freezer) — le conteneur reste "en cours" mais ne consomme plus de CPU.

Exercice 4 : 

cmd : docker restart mon_site

correction : docker unpause mon_site 

explication : Dégèle les processus et reprend l'exécution normale, exactement là où elle s'était arrêtée.

Exercice 5 : 

cmd : docker 

correction :  docker pull redis 

explication : Télécharge l'image (et toutes ses couches) depuis Docker Hub vers votre machine locale, sans créer de conteneur.

Exercice 6 : 

cmd : docker copy mon_site PATH=/usr/share/nginx/html/index.html

correction : docker cp mon_site:/usr/share/nginx/html/index.html

explication : Syntaxe générale : docker cp <source> <destination>. Ici la source est locale et la destination est nom_du_conteneur:chemin.

Exercice 7 : 

cmd : 

correction : docker cp mon_site:/etc/nginx/nginx.conf .

explication : 
Même logique, mais inversée : la source est nom_du_conteneur:chemin, et . représente le dossier courant sur votre machine.

Exercice 8 : 

cmd : docker run -d mysql -e MYSQL_ROOT_PASSWORD=secret123

correction : docker run -d -e MYSQL_ROOT_PASSWORD=secret123 mysql

explication : les bons termes sont là mais pas de bonne manière. -e (--env) permet de définir une variable d'environnement à l'intérieur du conteneur, au format CLE=valeur. On peut utiliser plusieurs -e pour définir plusieurs variables.

Exercice 9 : 

cmd : docker run nginx -m 256m

correction : docker run -d --memory=256m nginx

--memory (ou -m) fixe la quantité maximale de RAM utilisable par le conteneur. Si cette limite est dépassée, le conteneur peut être arrêté de force par le noyau (OOM killer).

Exercice 10 : 

cmd : 

correction : docker system prune

Supprime : tous les conteneurs arrêtés, tous les réseaux non utilisés par au moins un conteneur, toutes les images "dangling" (sans tag), et le cache de build inutilisé.
👉 Docker demande une confirmation avant de procéder (y/N).
👉 Astuce : ajouter -a (docker system prune -a) supprime en plus toutes les images non utilisées par un conteneur (pas seulement les "dangling"), ce qui est plus agressif.