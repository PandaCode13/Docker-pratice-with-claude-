Thème : Réseaux Docker en profondeur

Contexte : reprise du projet full-stack shop — une application composée d'un frontend React (shop_frontend), d'une API Node.js (shop_api), d'une base de données MySQL (shop_db), et d'un reverse-proxy Nginx (shop_proxy) qui redirige le trafic vers le frontend et l'API.

Exercice 1 — Modes réseau : bridge par défaut vs host
Lancez shop_api (image shop-api:1.0, port 4000) en mode détaché avec le driver réseau bridge par défaut, puis relancez-le une seconde fois sous le nom shop_api_host avec le driver host (le conteneur partage directement la pile réseau de la machine hôte, sans mapping de port). Expliquez en une phrase la différence de comportement du port 4000 entre les deux cas.

Exercice 2 — Mode réseau none
Lancez un conteneur shop_worker (image alpine, tâche de fond fictive) avec le driver réseau none. Expliquez en une phrase dans quel cas ce mode est pertinent pour un service comme un worker de traitement de fichiers.

Exercice 3 — Créer un réseau avec un sous-réseau personnalisé
Créez un réseau shop_backend_net avec le driver bridge, en lui imposant explicitement le sous-réseau 172.28.0.0/16.

Exercice 4 — Assigner une IP fixe à un conteneur
Relancez shop_db (MySQL) connecté à shop_backend_net, avec l'adresse IP fixe 172.28.0.10.

Exercice 5 — Alias réseau
Connectez shop_db au réseau shop_backend_net avec un alias réseau supplémentaire database, de sorte que shop_api puisse le joindre indifféremment via shop_db ou database.

Exercice 6 — Isoler le frontend de la base de données
Créez un second réseau shop_frontend_net. Connectez shop_proxy et shop_frontend sur shop_frontend_net uniquement, et shop_api sur les deux réseaux (shop_frontend_net et shop_backend_net), de sorte que le frontend ne puisse jamais atteindre directement shop_db — seule l'API le peut.

Exercice 7 — Vérifier l'isolation réseau
Depuis un terminal ouvert dans shop_proxy (via docker compose exec ou docker exec), écrivez la commande qui tente de joindre shop_db sur le port 3306, afin de vérifier que la connexion échoue bien (isolation correcte).

Exercice 8 — Résolution DNS interne
Depuis un terminal ouvert dans shop_api, écrivez la commande qui interroge le DNS interne de Docker pour résoudre l'adresse IP associée au nom shop_db.

Exercice 9 — Inspecter le trafic entre deux conteneurs
Depuis la machine hôte, écrivez la commande docker network inspect permettant d'afficher, au format JSON, la liste des conteneurs connectés à shop_backend_net avec leurs adresses IP respectives.

Exercice 10 — Débrancher un conteneur d'un réseau à chaud
Sans arrêter shop_api, déconnectez-le du réseau shop_frontend_net (il doit rester connecté à shop_backend_net).

Exercice 11 — Réseau dédié pour un outil de debug
Créez un conteneur shop_debug (image nicolaka/netshoot, une image dédiée aux outils réseau) connecté à shop_backend_net, en mode interactif, afin de pouvoir diagnostiquer les problèmes de connectivité vers shop_db sans polluer les conteneurs applicatifs.

Exercice 12 — Capturer le trafic réseau
Depuis shop_debug (exercice 11), écrivez la commande tcpdump permettant de capturer le trafic réseau à destination du port 3306 (MySQL), afin de vérifier que l'API dialogue bien avec la base.

Exercice 13 — Lister tous les réseaux liés au projet
Écrivez la commande permettant de lister uniquement les réseaux Docker dont le nom contient shop.

Exercice 14 — Réseau en lecture d'un docker-compose.yml existant
Vous avez un docker-compose.yml définissant les réseaux shop_frontend_net et shop_backend_net. Écrivez la commande permettant de connaître le nom réel généré par Compose pour shop_backend_net (Compose préfixe généralement les noms de réseaux avec le nom du projet).

Exercice 15 — Nettoyer les réseaux orphelins
Après plusieurs tests, vous avez accumulé des réseaux Docker non utilisés par aucun conteneur. Écrivez la commande permettant de les supprimer tous en une fois, sans toucher aux réseaux actuellement utilisés.