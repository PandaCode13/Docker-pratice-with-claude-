🎯 Projet : Stack Blog avec Nginx + PHP + MySQL

Contexte : tu vas monter une stack web classique composée de trois conteneurs : un serveur Nginx (pour servir les pages), un serveur PHP (pour traiter le code), et une base de données MySQL, tous reliés par un réseau Docker, avec persistance des données.

📋 Cahier des charges

Partie 1 — Préparation

Créez un réseau Docker nommé blog_network.
Créez un volume nommé blog_db_data pour persister les données MySQL.
Créez un volume nommé blog_files pour stocker les fichiers du blog.

Partie 2 — Base de données
4. Lancez un conteneur MySQL nommé blog_db, connecté au réseau blog_network, avec le volume blog_db_data monté sur /var/lib/mysql, et les variables d'environnement suivantes :

MYSQL_ROOT_PASSWORD : rootpass
MYSQL_DATABASE : blog
MYSQL_USER : bloguser
MYSQL_PASSWORD : blogpass
Vérifiez que le conteneur blog_db tourne correctement.
Consultez les logs de blog_db et confirmez que MySQL est prêt à accepter des connexions.

Partie 3 — Serveur PHP (Dockerfile)
7. Créez un dossier app/ contenant :

Un fichier index.php qui affiche "Bienvenue sur mon blog Docker" dans une balise <h1>.
Un Dockerfile qui :
part de l'image php:8.1-cli
définit /app comme répertoire de travail
copie les fichiers du projet
expose le port 8000
lance php -S 0.0.0.0:8000 au démarrage
Construisez cette image en la nommant blog-php:1.0.

Partie 4 — Serveur Nginx
9. Lancez un conteneur nginx nommé blog_nginx, connecté au réseau blog_network, avec le port 8080 de l'hôte publié vers le port 80 du conteneur.
10. Vérifiez dans votre navigateur que http://localhost:8080 affiche bien la page d'accueil Nginx.

Partie 5 — Lancement PHP
11. Lancez un conteneur nommé blog_php à partir de l'image blog-php:1.0, connecté au réseau blog_network, avec le port 8000 publié vers l'hôte.
12. Vérifiez dans votre navigateur que http://localhost:8000 affiche bien "Bienvenue sur mon blog Docker".

Partie 6 — Vérifications & manipulations
13. Listez tous les conteneurs en cours d'exécution et vérifiez que les trois (blog_db, blog_nginx, blog_php) apparaissent bien.
14. Depuis le conteneur blog_php, pinguez le conteneur blog_db par son nom pour vérifier qu'ils communiquent bien.
15. Copiez le fichier index.php du conteneur blog_php vers un dossier local sauvegarde/ sur votre machine.
16. Inspectez le conteneur blog_db et retrouvez son adresse IP interne sur blog_network.

Partie 7 — Nettoyage
17. Arrêtez et supprimez les trois conteneurs en une seule commande chacun.
18. Supprimez le réseau blog_network.
19. Supprimez le volume blog_files (vide, inutilisé).
20. Faut-il supprimer le volume blog_db_data ? Justifiez.