Exercice 1 — Créer un volume

Écrivez la commande pour créer un volume Docker nommé mes_donnees.

cmd : docker volume create mes_donnees

correction : docker volume create mes_donnees

explication : Crée un volume géré par Docker, stocké dans une zone dédiée du système (généralement /var/lib/docker/volumes/ sur Linux), indépendamment de tout conteneur.

Exercice 2 — Lister les volumes
Écrivez la commande pour afficher la liste de tous les volumes présents sur votre machine.

cmd : docker volumes 

correction : docker volume ls

Exercice 3 — Utiliser un volume dans un conteneur
Écrivez une commande docker run qui lance un conteneur à partir de l'image mysql en mode détaché, en montant le volume mes_donnees sur le chemin /var/lib/mysql à l'intérieur du conteneur.

cmd : docker run -v mes_donnees=/var/lib/mysql mysql

correction : docker run -d -v mes_donnees:/var/lib/mysql mysql

explication : 

-v (--volume) : format nom_du_volume:chemin_dans_le_conteneur.
Si mes_donnees n'existe pas encore, Docker le crée automatiquement au lancement.
Avantage : même si le conteneur est supprimé, les données de la base restent intactes dans le volume, réutilisables par un nouveau conteneur.

Exercice 4 — Inspecter un volume
Écrivez la commande pour afficher les détails (au format JSON) du volume mes_donnees.

cmd : docker inspect -v mes_donnees 

correction : docker volume inspect mes_donnees

explication : Retourne des infos comme le chemin réel sur le disque hôte (Mountpoint), le driver utilisé, et la date de création.

Exercice 5 — Supprimer un volume
Écrivez la commande pour supprimer le volume mes_donnees (attention : aucun conteneur ne doit l'utiliser).

cmd : docker rmv mes_donnees 

correction : docker volume rm mes_donnees

explication : Supprime définitivement le volume et toutes les données qu'il contient. Si un conteneur (même arrêté) l'utilise encore, Docker refuse avec une erreur.

Exercice 6 — Bind mount (montage local)
Écrivez une commande docker run qui lance un conteneur nginx en mode détaché, en montant le dossier local /home/user/site sur /usr/share/nginx/html à l'intérieur du conteneur (montage direct depuis votre système de fichiers, pas un volume nommé).

cmd : cmd : docker run -d nginx -v /usr/share/nginx/html:/home/user/site 

correction : docker run -d -v /home/user/site:/usr/share/nginx/html nginx

explication : Différence clé avec l'Exercice 3 : ici, /home/user/site est un chemin absolu de votre machine, pas un nom de volume géré par Docker. C'est un bind mount : le conteneur voit directement le contenu de ce dossier, et toute modification (dans un sens ou dans l'autre) est immédiate et bidirectionnelle. Très utilisé en développement pour éditer du code en local et voir les changements reflétés instantanément dans le conteneur.

Exercice 7 — Créer un réseau
Écrivez la commande pour créer un réseau Docker nommé mon_reseau.

cmd : docker network create mon_reseau

correction : docker network create mon_reseau

explication : 
Crée un réseau Docker (driver bridge par défaut), permettant à plusieurs conteneurs de communiquer entre eux par leur nom (résolution DNS automatique).

Exercice 8 — Lister les réseaux
Écrivez la commande pour afficher la liste de tous les réseaux Docker disponibles.


cmd : docker network ls 

correction : tout est correct Affiche tous les réseaux disponibles, y compris les réseaux par défaut créés automatiquement par Docker (bridge, host, none).

Exercice 9 — Lancer un conteneur sur un réseau spécifique
Écrivez une commande docker run qui lance un conteneur nginx en mode détaché, connecté au réseau mon_reseau.

cmd : docker run -d -n mon_reseau nginx 

correction : docker run -d --network mon_reseau nginx

explication : 
--network connecte le conteneur au réseau spécifié dès sa création. Deux conteneurs sur le même réseau personnalisé peuvent alors se joindre directement via leur nom (ex : ping nginx depuis un autre conteneur du même réseau).

Exercice 10 — Supprimer un réseau
Écrivez la commande pour supprimer le réseau mon_reseau (attention : aucun conteneur ne doit y être connecté).

cmd : docker rmn mon_reseau 

correction : docker network rm mon_reseau

explication : Supprime le réseau. Comme pour les volumes, si un conteneur y est encore connecté, la suppression échoue — il faut d'abord le déconnecter (docker network disconnect) ou arrêter/supprimer le conteneur concerné.