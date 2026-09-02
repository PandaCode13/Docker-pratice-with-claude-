🎯 Projet : Stack API + Cache Redis

Contexte : tu vas monter une architecture composée d'une API Node.js et d'un serveur de cache Redis, reliés par un réseau Docker, avec persistance des données du cache.

📋 Cahier des charges

Partie 1 — Préparation

Créez un réseau Docker nommé api_network.
Créez un volume nommé redis_data pour persister les données de Redis.

Partie 2 — Cache Redis
3. Lancez un conteneur redis nommé api_redis, connecté au réseau api_network, avec le volume redis_data monté sur /data, en mode détaché.
4. Vérifiez que le conteneur api_redis tourne correctement.
5. Consultez les logs de api_redis et confirmez que Redis a bien démarré (cherchez une ligne mentionnant Ready to accept connections).

Partie 3 — API Node.js (Dockerfile)
6. Créez un dossier api/ contenant :

Un fichier package.json minimal déclarant une dépendance à redis (le client npm).
Un fichier server.js qui :
démarre un serveur HTTP sur le port 4000
à chaque requête sur /, incrémente un compteur stocké dans Redis (clé visites) et retourne "Nombre de visites : X"
Un Dockerfile qui :
part de l'image node:18
définit /app comme répertoire de travail
copie les fichiers du projet
installe les dépendances avec npm install
expose le port 4000
lance node server.js au démarrage
Construisez cette image en la nommant api-app:1.0.

Partie 4 — Lancement de l'API
8. Lancez un conteneur nommé api_app à partir de l'image api-app:1.0, connecté au réseau api_network, avec le port 4000 publié vers l'hôte, et une variable d'environnement REDIS_HOST définie à api_redis (pour que l'API sache où trouver Redis).
9. Vérifiez dans votre navigateur (ou avec curl) que http://localhost:4000 répond bien, et que le compteur s'incrémente à chaque rechargement.

Partie 5 — Vérifications & manipulations
10. Listez tous les conteneurs en cours d'exécution et vérifiez que api_app et api_redis apparaissent bien.
11. Depuis le conteneur api_app, pinguez le conteneur api_redis par son nom pour vérifier qu'ils communiquent bien.
12. Copiez le fichier server.js du conteneur api_app vers un dossier local sauvegarde_api/ sur votre machine.
13. Inspectez le conteneur api_redis et retrouvez son adresse IP interne sur api_network.
14. Limitez a posteriori la consommation mémoire du conteneur api_app à 200 Mo. Est-ce possible directement sur le conteneur existant ? Si non, quelles commandes utiliser pour corriger la situation ?

Partie 6 — Nettoyage
15. Arrêtez et supprimez les deux conteneurs en une seule commande chacun.
16. Supprimez le réseau api_network.
17. Le volume redis_data doit-il être supprimé ? Justifiez votre réponse.
18. Supprimez les images api-app:1.0 et redis (uniquement si elles ne sont plus utilisées par aucun conteneur).