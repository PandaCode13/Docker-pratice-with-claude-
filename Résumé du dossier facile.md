# 📦 Récapitulatif des TP Docker

Ce document résume l'ensemble des travaux pratiques réalisés pour apprendre les bases de Docker, du lancement d'un premier conteneur jusqu'à l'écriture d'un Dockerfile complet.

---

## TP1 — Premiers pas avec `docker run`

**Objectif** : lancer, nommer, publier et supprimer un conteneur simple.

| # | Exercice | Commande clé |
|---|----------|---------------|
| 1 | Lancer un conteneur en arrière-plan | `docker run -d nginx` |
| 2 | Nommer un conteneur | `docker run --name mon_site -d nginx` |
| 3 | Publier un port | `docker run --name mon_site -d -p 8080:80 nginx` |
| 4 | Lister les conteneurs actifs | `docker ps` |
| 5 | Arrêter puis supprimer | `docker stop mon_site` / `docker rm mon_site` |

**Notions clés** : mode détaché (`-d`), nommage (`--name`), publication de ports (`-p hôte:conteneur`).

---

## TP2 — Logs, exécution et images

**Objectif** : inspecter le comportement d'un conteneur en cours d'exécution et gérer les images.

| # | Exercice | Commande clé |
|---|----------|---------------|
| 1 | Voir les logs | `docker logs mon_site` |
| 2 | Suivre les logs en direct | `docker logs -f mon_site` |
| 3 | Ouvrir un terminal dans le conteneur | `docker exec -it mon_site bash` |
| 4 | Lister les images locales | `docker images` |
| 5 | Supprimer une image | `docker rmi nginx` |

**Notions clés** : `-f`/`--follow`, `-it` (interactif + pseudo-terminal), différence conteneur/image.

---

## TP3 — Cycle de vie, ressources et transfert de fichiers

**Objectif** : manipuler l'état d'un conteneur (pause, redémarrage), transférer des fichiers, et limiter ses ressources.

| # | Exercice | Commande clé |
|---|----------|---------------|
| 1 | Inspecter un conteneur (JSON) | `docker inspect mon_site` |
| 2 | Redémarrer | `docker restart mon_site` |
| 3 | Mettre en pause | `docker pause mon_site` |
| 4 | Reprendre | `docker unpause mon_site` |
| 5 | Télécharger une image sans la lancer | `docker pull redis` |
| 6 | Copier un fichier vers le conteneur | `docker cp index.html mon_site:/usr/share/nginx/html/index.html` |
| 7 | Copier un fichier depuis le conteneur | `docker cp mon_site:/etc/nginx/nginx.conf .` |
| 8 | Variable d'environnement | `docker run -d -e MYSQL_ROOT_PASSWORD=secret123 mysql` |
| 9 | Limiter la mémoire | `docker run -d --memory=256m nginx` |
| 10 | Nettoyage global du système | `docker system prune` |

**Notions clés** : `pause`/`unpause` (gel via cgroups), `docker cp` (bidirectionnel), `--memory`, `system prune`.

---

## TP4 — Volumes et réseaux

**Objectif** : assurer la persistance des données et faire communiquer plusieurs conteneurs entre eux.

| # | Exercice | Commande clé |
|---|----------|---------------|
| 1 | Créer un volume | `docker volume create mes_donnees` |
| 2 | Lister les volumes | `docker volume ls` |
| 3 | Utiliser un volume | `docker run -d -v mes_donnees:/var/lib/mysql mysql` |
| 4 | Inspecter un volume | `docker volume inspect mes_donnees` |
| 5 | Supprimer un volume | `docker volume rm mes_donnees` |
| 6 | Bind mount (montage local) | `docker run -d -v /home/user/site:/usr/share/nginx/html nginx` |
| 7 | Créer un réseau | `docker network create mon_reseau` |
| 8 | Lister les réseaux | `docker network ls` |
| 9 | Lancer un conteneur sur un réseau | `docker run -d --network mon_reseau nginx` |
| 10 | Supprimer un réseau | `docker network rm mon_reseau` |

**Notions clés** : volume nommé (géré par Docker) vs bind mount (chemin local), résolution DNS automatique entre conteneurs d'un même réseau.

---

## TP5 — Écrire et construire un Dockerfile

**Objectif** : créer sa propre image personnalisée à partir d'un `Dockerfile`.

| # | Exercice | Instruction / commande clé |
|---|----------|------------------------------|
| 1 | Image de base | `FROM node:18` |
| 2 | Répertoire de travail | `WORKDIR /app` |
| 3 | Copier les fichiers | `COPY . .` |
| 4 | Installer les dépendances (au build) | `RUN npm install` |
| 5 | Exposer un port (documentaire) | `EXPOSE 3000` |
| 6 | Commande de démarrage | `CMD ["node", "app.js"]` |
| 7 | Construire l'image | `docker build -t mon-app .` |
| 8 | Construire avec un tag précis | `docker build -t mon-app:1.0 .` |
| 9 | Lancer un conteneur depuis l'image | `docker run -d -p 3000:3000 mon-app:1.0` |
| 10 | Variable d'environnement dans l'image | `ENV NODE_ENV=production` |

**Notions clés** : différence `RUN` (au build) vs `CMD` (au démarrage), `EXPOSE` ne publie rien seul, tagging des images (`nom:tag`).

---

## 🧩 Projets de synthèse

En complément des 5 TP, trois **mini-projets** ont permis de combiner toutes ces commandes dans des architectures multi-conteneurs réalistes :

1. **Mini stack web** : app Node.js (Dockerfile) + MySQL, reliées par un réseau, avec volume de persistance.
2. **Stack Blog** : Nginx + PHP (Dockerfile) + MySQL, avec plusieurs variables d'environnement et volumes.
3. **Stack API + Cache Redis** : API Node.js (Dockerfile) + Redis, avec compteur persistant et vérification de communication inter-conteneurs.

Chaque projet suit le même schéma pédagogique : réseau → volume(s) → base de données → Dockerfile de l'app → lancement → vérifications (`ping`, `inspect`, `cp`) → nettoyage.

---

## 📚 Progression globale

```
TP1 : run, name, port, ps, stop/rm
   ↓
TP2 : logs, exec, images
   ↓
TP3 : inspect, restart, pause, cp, env, memory, prune
   ↓
TP4 : volumes, réseaux (persistance + communication)
   ↓
TP5 : Dockerfile (créer ses propres images)
   ↓
Projets : combiner le tout dans des stacks multi-conteneurs
```

**Prochaine étape suggérée** : `docker-compose`, pour remplacer l'enchaînement de commandes `docker run` par un seul fichier `docker-compose.yml` déclaratif.