Thème : cycle de vie avancé des images, ressources, sécurité et diagnostic

Exercice 1 — Créer une image à partir d'un conteneur modifié
Lancez un conteneur ubuntu en mode interactif, installez-y curl à l'intérieur (apt update && apt install -y curl), puis, sans écrire de Dockerfile, créez une nouvelle image nommée ubuntu-curl:1.0 à partir de l'état de ce conteneur.

Exercice 2 — Voir les modifications d'un conteneur
Avant de committer, écrivez la commande qui liste les fichiers ajoutés, modifiés ou supprimés dans le conteneur de l'exercice 1 par rapport à l'image ubuntu d'origine.

Exercice 3 — Exporter une image en fichier
Écrivez la commande pour exporter l'image ubuntu-curl:1.0 dans un fichier ubuntu-curl.tar, afin de pouvoir la transférer sur une machine sans accès à Docker Hub.

Exercice 4 — Importer une image depuis un fichier
Sur une autre machine, écrivez la commande pour charger l'image contenue dans ubuntu-curl.tar dans le registre local Docker.

Exercice 5 — Limiter le CPU d'un conteneur
Lancez un conteneur nginx en mode détaché en limitant son utilisation à 0.5 CPU (la moitié d'un cœur).

Exercice 6 — Conteneur en lecture seule
Relancez ce conteneur nginx avec un filesystem racine en lecture seule, tout en autorisant l'écriture temporaire dans /tmp via un tmpfs.

Exercice 7 — Retirer des capabilities Linux
Lancez un conteneur nginx en mode détaché en lui retirant toutes les capabilities Linux, puis en ne réajoutant que celle strictement nécessaire pour qu'il puisse écouter sur un port privilégié (NET_BIND_SERVICE).

Exercice 8 — HEALTHCHECK dans le Dockerfile
Écrivez un Dockerfile pour une image nginx:alpine qui inclut une instruction HEALTHCHECK native vérifiant toutes les 15 secondes que curl -f http://localhost/ répond, avec 3 tentatives avant de déclarer le conteneur unhealthy.

Exercice 9 — LABEL de métadonnées
Dans ce même Dockerfile, ajoutez des LABEL indiquant le mainteneur (maintainer) et la version de l'image (version="1.0").

Exercice 10 — Connecter un conteneur déjà lancé à un réseau
Vous avez un conteneur shop_api déjà démarré sur le réseau par défaut. Sans le relancer, connectez-le en plus au réseau shop_network.

Exercice 11 — Inspecter les connexions réseau
Écrivez la commande pour afficher, au format JSON, tous les conteneurs actuellement connectés au réseau shop_network.

Exercice 12 — Espace disque utilisé par Docker
Écrivez la commande qui affiche un résumé de l'espace disque total utilisé par les images, conteneurs et volumes sur votre machine.

Exercice 13 — Historique des couches d'une image
Écrivez la commande permettant de visualiser le détail des couches (layers) de l'image ubuntu-curl:1.0, avec la taille de chacune.

Exercice 14 — Logs avec filtres
Le conteneur shop_api tourne depuis plusieurs heures. Écrivez la commande pour n'afficher que les 50 dernières lignes de ses logs, avec horodatage.

Exercice 15 — Limiter la taille des logs
Relancez shop_api en configurant son driver de logs pour ne conserver au maximum que 3 fichiers de 10 Mo chacun (rotation automatique).