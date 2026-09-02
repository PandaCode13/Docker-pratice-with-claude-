# 📘 Cours Docker — De A à Z
### Synthèse de tous les TP (TP1 → TP9 + TP Final)

Ce document rassemble, par thème, toutes les commandes vues et corrigées au fil des TP. Il sert de mémo/cours complet à garder sous la main.

---

## Sommaire

1. [Lancer, nommer, publier un conteneur](#1)
2. [Cycle de vie d'un conteneur](#2)
3. [Logs et exécution dans un conteneur](#3)
4. [Gestion des images](#4)
5. [Volumes : persister les données](#5)
6. [Bind mounts](#6)
7. [Réseaux Docker](#7)
8. [Écrire un Dockerfile](#8)
9. [Bonnes pratiques Dockerfile](#9)
10. [ARG vs ENV](#10)
11. [Docker Compose](#11)
12. [Précédence des variables d'environnement](#12)
13. [Publier sur Docker Hub](#13)
14. [Monitoring et nettoyage](#14)
15. [Pense-bête — toutes les commandes](#15)

---

<a id="1"></a>
## 1. Lancer, nommer, publier un conteneur

### Lancer un conteneur simple, en détaché
```bash
docker run -d nginx
```
`-d` (detached) : le conteneur tourne en arrière-plan, tu récupères la main immédiatement.

### Nommer le conteneur
```bash
docker run --name mon_site -d nginx
```
⚠️ Piège classique : `--name` s'utilise **au moment du `docker run`**. `docker rename ancien_nom nouveau_nom` existe aussi, mais c'est une commande différente qui renomme un conteneur **déjà existant** — elle ne permet pas de "corriger après coup" un `docker run` mal écrit si le conteneur n'a jamais été créé avec le bon nom dès le départ, et surtout n'ajoute aucune option (port, réseau, volume...).

### Publier un port
```bash
docker run --name mon_site -d -p 8080:80 nginx
```
Format : `-p <port_hôte>:<port_conteneur>`. Ici, `localhost:8080` (ta machine) redirige vers le port `80` à l'intérieur du conteneur (celui où Nginx écoute).
⚠️ `-p 8080` seul (sans le `:`) ne fait **pas** la même chose : Docker publierait le port conteneur sur un port hôte **aléatoire**.

### Lister les conteneurs en cours d'exécution
```bash
docker ps
```
> `docker ps -a` liste **tous** les conteneurs, y compris ceux arrêtés — à ne pas confondre avec `docker ps` seul si la question demande spécifiquement les conteneurs **actifs**.

### Arrêter puis supprimer un conteneur
```bash
docker stop mon_site
docker rm mon_site
```
Astuce : `docker rm -f mon_site` fait les deux en une seule commande (stop forcé + suppression).

---

<a id="2"></a>
## 2. Cycle de vie d'un conteneur

| Commande | Effet |
|---|---|
| `docker stop <c>` | Arrête proprement (signal SIGTERM puis SIGKILL après un délai) |
| `docker start <c>` | Redémarre un conteneur arrêté |
| `docker restart <c>` | Équivaut à `stop` + `start` en une seule commande |
| `docker pause <c>` | Gèle tous les processus du conteneur (cgroups freezer) — il reste "en cours" mais ne consomme plus de CPU |
| `docker unpause <c>` | Dégèle et reprend l'exécution exactement où elle s'était arrêtée |

⚠️ Piège vu en TP3 : `docker stop start mon_site` n'existe pas — les deux actions ne se chaînent pas en un seul mot, la bonne commande est `docker restart mon_site`.

⚠️ Piège vu au TP Final : `docker restart` (comme `docker start`/`docker stop`) ne fait que rejouer le conteneur **tel qu'il a été créé**. Il n'accepte **aucune** option comme `--network`, `-v` ou `--memory`. Pour changer ces paramètres, il faut :
- soit **supprimer et recréer** le conteneur avec `docker run` et les bonnes options,
- soit utiliser `docker update` pour certains réglages modifiables à chaud (mémoire par exemple) :
```bash
docker update --memory="512m" --memory-swap="512m" mon_conteneur
```

### Inspecter un conteneur
```bash
docker inspect mon_site
```
Retourne un JSON complet : configuration réseau, volumes montés, variables d'environnement, chemins internes, etc. (`docker inspect` fonctionne aussi pour les images, volumes et réseaux, mais `docker volume inspect` / `docker network inspect` restent plus lisibles pour ces objets-là).

---

<a id="3"></a>
## 3. Logs et exécution dans un conteneur

### Voir les logs
```bash
docker logs mon_site
docker logs -f mon_site      # suivi en direct (follow)
```
⚠️ `docker logs -a` n'existe pas — l'option de suivi en direct est `-f`, pas `-a`.

### Ouvrir un terminal dans un conteneur
```bash
docker exec -it mon_site bash
```
- `-i` (interactive) + `-t` (pseudo-TTY) : nécessaires ensemble pour un vrai terminal interactif.
- ⚠️ Toutes les images n'ont pas `bash` ! Les images basées sur **Alpine** (`node:18-alpine`, etc.) n'embarquent que `sh` (BusyBox) par défaut. `bash` fonctionne en revanche sur les images basées Debian/Ubuntu (`nginx`, `mysql`, `node:18`...).

### Copier des fichiers entre hôte et conteneur
```bash
docker cp mon_site:/etc/nginx/nginx.conf .        # conteneur → hôte (le "." = dossier courant)
docker cp dump.sql mon_site:/tmp/dump.sql         # hôte → conteneur
```
Syntaxe générale : `docker cp <source> <destination>`. Le côté qui contient `nom_conteneur:` est celui interne au conteneur.
⚠️ La commande s'appelle `docker cp`, jamais `docker copy` (piège récurrent).

---

<a id="4"></a>
## 4. Gestion des images

| Commande | Effet |
|---|---|
| `docker images` (ou `docker image ls`) | Liste toutes les images locales |
| `docker pull <image>` | Télécharge une image depuis Docker Hub sans créer de conteneur |
| `docker rmi <image>` | Supprime une image (⚠️ pas `docker rm`, réservé aux conteneurs) |
| `docker build -t <nom>:<tag> .` | Construit une image à partir d'un Dockerfile dans le dossier courant |

**`rm` vs `rmi`** : `rm` supprime des **conteneurs**, `rmi` supprime des **images**. Une image ne peut être supprimée que si aucun conteneur (même arrêté) ne l'utilise encore.

---

<a id="5"></a>
## 5. Volumes : persister les données

```bash
docker volume create mes_donnees
docker volume ls
docker volume inspect mes_donnees
docker volume rm mes_donnees
```
- `docker volume inspect` (et non `docker inspect -v`, qui n'est pas une syntaxe valide) retourne notamment le champ `"Mountpoint"` : l'emplacement réel du volume sur le disque hôte (souvent `/var/lib/docker/volumes/<nom>/_data`).
- `docker volume rm` échoue tant qu'un conteneur (même arrêté) référence le volume.

### Monter un volume dans un conteneur
```bash
docker run -d -v mes_donnees:/var/lib/mysql mysql
```
Format : `-v nom_du_volume:chemin_dans_le_conteneur`.
⚠️ Ne pas confondre avec `-v mes_donnees=/var/lib/mysql` (le séparateur est `:`, pas `=`).
Si le volume n'existe pas encore, Docker le crée automatiquement.
Avantage : même si le conteneur est supprimé, les données survivent dans le volume, réutilisables par un nouveau conteneur.

---

<a id="6"></a>
## 6. Bind mounts

```bash
docker run -d -v /home/user/site:/usr/share/nginx/html nginx
```
Différence clé avec un volume nommé : ici, `/home/user/site` est un **chemin absolu de la machine hôte**, pas un objet géré par Docker. C'est un montage direct — toute modification (dans un sens comme dans l'autre) est immédiate et bidirectionnelle. Très utilisé en développement pour éditer du code en local et voir les changements reflétés instantanément dans le conteneur.

⚠️ L'ordre des arguments compte : c'est toujours `-v <chemin_hôte>:<chemin_conteneur>`, jamais l'inverse.

---

<a id="7"></a>
## 7. Réseaux Docker

```bash
docker network create mon_reseau
docker network ls
docker network rm mon_reseau
```
`docker network ls` affiche aussi les réseaux par défaut créés automatiquement (`bridge`, `host`, `none`).

### Connecter un conteneur à un réseau
```bash
docker run -d --network mon_reseau nginx
```
⚠️ `-n` n'est **pas** un alias valide de `--network` (piège vu plusieurs fois) — il faut écrire `--network` (ou `--net`) en toutes lettres.

**Intérêt principal** : deux conteneurs sur le même réseau personnalisé (bridge custom) peuvent se joindre directement **par leur nom** grâce à la résolution DNS automatique intégrée à Docker (ex : `ping nom_du_conteneur` fonctionne, sans connaître son IP).

`docker network rm` échoue si un conteneur y est encore connecté — il faut d'abord le déconnecter ou le supprimer.

---

<a id="8"></a>
## 8. Écrire un Dockerfile

Structure de base (exemple Node.js) :
```dockerfile
FROM node:18
WORKDIR /app
COPY . .
RUN npm install
EXPOSE 3000
CMD ["node", "app.js"]
```

| Instruction | Rôle |
|---|---|
| `FROM` | Image de base à partir de laquelle construire |
| `WORKDIR` | Définit le répertoire de travail dans le conteneur (créé s'il n'existe pas) |
| `COPY <src> <dst>` | Copie des fichiers du contexte de build vers l'image |
| `RUN` | Exécute une commande **au moment du build** (installe des paquets, etc.) |
| `EXPOSE` | Documente le port utilisé par l'application (n'ouvre rien à lui seul, c'est `-p` au `run` qui publie réellement) |
| `CMD` | Commande exécutée **au démarrage** du conteneur (forme exec recommandée : `["node", "app.js"]`) |

### Construire et lancer
```bash
docker build -t mon-app:1.0 .
docker run -d -p 3000:3000 mon-app:1.0
```

---

<a id="9"></a>
## 9. Bonnes pratiques Dockerfile

### a) Optimiser le cache de build

```dockerfile
FROM node:18-alpine
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm install
COPY . .
CMD ["node", "app.js"]
```
**Pourquoi séparer `COPY package*.json` de `COPY . .` ?**
Docker met en cache chaque instruction. Tant que `package.json` ne change pas, Docker réutilise le cache de l'étape `npm install` (souvent la plus longue) même si tu modifies seulement du code source. Avec un `COPY . .` unique placé *avant* `RUN npm install`, la moindre modification de code invaliderait tout le cache et forcerait une réinstallation complète à chaque build.

### b) `.dockerignore`
```
node_modules
.env
```
Évite de copier des fichiers volumineux, inutiles, ou sensibles dans le contexte de build (et empêche un `node_modules` local d'écraser celui installé dans l'image).

### c) Multi-stage build

```dockerfile
# --- Étape 1 : builder ---
FROM node:18 AS builder
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm install
COPY . .
RUN npm run build

# --- Étape 2 : image finale ---
FROM nginx:alpine
COPY --from=builder /app/dist /usr/share/nginx/html
```
**Pourquoi ?** L'image finale ne contient que le strict nécessaire à l'exécution (ici Nginx + fichiers statiques compilés), sans les outils, dépendances et fichiers intermédiaires utilisés uniquement pendant le build. Résultat : image plus **légère** et plus **sûre** (surface d'attaque réduite).
⚠️ Piège fréquent : oublier `RUN npm install` dans l'étape builder, ou oublier le deuxième `FROM` — sans les **deux** `FROM` (un pour builder, un pour l'image finale), ce n'est pas un multi-stage build.

### d) Utilisateur non-root
```dockerfile
FROM node:18-alpine
WORKDIR /app
COPY . .
RUN npm install
USER node
CMD ["node", "app.js"]
```
`USER node` fait en sorte que toutes les instructions suivantes et le processus final s'exécutent avec les droits de l'utilisateur `node` plutôt qu'en `root`. Bonne pratique de sécurité : en cas de compromission de l'application, l'attaquant n'a pas les pleins pouvoirs dans le conteneur.
👉 L'utilisateur `node` existe déjà par défaut dans les images officielles Node.js. Sur une image qui n'a pas cet utilisateur prédéfini (comme Alpine "nu"), il faut le créer soi-même :
```dockerfile
RUN addgroup -S appgroup && adduser -S appuser -G appgroup
USER appuser
```

### e) Minimiser le nombre de couches / nettoyer dans le même `RUN`
```dockerfile
RUN apt-get update && apt-get install -y curl && rm -rf /var/lib/apt/lists/*
```
Chaque `RUN` crée une nouvelle couche, **conservée définitivement** même si des fichiers sont supprimés dans un `RUN` séparé plus tard. En combinant `update`, `install` et le nettoyage du cache dans une seule instruction reliée par `&&`, le nettoyage a lieu **avant** que la couche ne soit "figée" — ce qui réduit réellement la taille finale de l'image.

---

<a id="10"></a>
## 10. ARG vs ENV

```dockerfile
ARG NODE_VERSION=18
FROM node:${NODE_VERSION}
```
```bash
docker build --build-arg NODE_VERSION=20 -t mon-app:1.0 .
```

**Différence principale (disponibilité / persistance) :**
- `ARG` n'existe **que pendant la construction** de l'image (accessible uniquement dans le Dockerfile). Une fois le conteneur démarré, `docker exec ... env` ne l'affichera **pas** — la variable a disparu après le build.
- `ENV` définit une variable qui **persiste dans l'image finale** et reste disponible dans tous les conteneurs lancés à partir de cette image (visible avec `docker exec ... env`).

Pour "sauver" une valeur d'`ARG` au runtime, il faut la recopier explicitement dans une `ENV` :
```dockerfile
ARG NODE_ENV=production
ENV NODE_ENV=$NODE_ENV
```

---

<a id="11"></a>
## 11. Docker Compose

### a) Syntaxe de base d'un `docker-compose.yml`

```yaml
version: "3.8"
services:
  web:
    image: nginx
    ports:
      - "8080:80"
    depends_on:
      - db

  db:
    image: mysql
    environment:
      - MYSQL_ROOT_PASSWORD=secret123
    volumes:
      - db_data:/var/lib/mysql

volumes:
  db_data:
```

**Erreurs YAML classiques à éviter :**

| Piège | Pourquoi c'est faux | Bonne syntaxe |
|---|---|---|
| `services :` | Pas d'espace avant `:` en YAML | `services:` |
| `- image : nginx` | `image:` n'est pas une liste, pas de `-` devant | `image: nginx` |
| `port: 8080:80` | Mauvaise clé (singulier) + pas une liste | `ports:` puis `- "8080:80"` |
| `depends_on:` suivi de `db` sans tiret | `depends_on:` en syntaxe simple est une **liste** | `depends_on:` puis `- db` |
| `MYSQL_ROOT_PASSWORD = secret123` | Espaces autour du `=` | `MYSQL_ROOT_PASSWORD=secret123` |
| `volume:` | Mauvaise clé (singulier) | `volumes:` |
| `network:` | Clé inexistante en Compose (ni au niveau service ni racine) | `networks:` |
| `- shop_network` sans espace après le tiret | Pas reconnu comme élément de liste | `- shop_network` (espace obligatoire) |
| `image: MySQL` | Les noms d'image Docker Hub doivent être en minuscules | `image: mysql` |

**`depends_on` avec `condition` (attend un vrai healthcheck, pas juste "conteneur démarré") :**
```yaml
services:
  api:
    depends_on:
      db:
        condition: service_healthy
  db:
    image: mysql
    healthcheck:
      test: ["CMD", "mysqladmin", "ping", "-h", "localhost"]
      interval: 10s
      timeout: 5s
      retries: 5
```
⚠️ Piège : `depends_on:` ne peut pas mélanger le format **liste** (`- db`) et le format **mapping** (`db: \n condition: ...`) sous la même clé. Pour utiliser `condition:`, il faut passer entièrement au format mapping.

### b) Commandes `docker compose`

| Action | Commande | Différence avec l'équivalent "simple" |
|---|---|---|
| Lancer toute la stack en arrière-plan | `docker compose up -d` | `docker run` ne lance qu'**un seul** conteneur et ne lit pas de fichier Compose |
| Voir l'état des services | `docker compose ps` | `docker ps` montre **tous** les conteneurs de la machine, pas seulement ceux du projet |
| Voir les logs de tous les services | `docker compose logs` (`-f` pour suivre) | `docker logs` exige un nom de conteneur précis, un seul à la fois |
| Terminal dans un service | `docker compose exec api sh` | Cible le service par son **nom logique**, quel que soit le nom réel du conteneur généré |
| Redémarrer un seul service | `docker compose restart api` | `docker restart api` cherche un conteneur nommé littéralement `api`, ce qui peut ne pas exister |
| Consommation CPU/RAM en direct | `docker stats` (ou `docker stats $(docker compose ps -q)` pour ne cibler que la stack) | — |
| Lancer plusieurs instances d'un service | `docker compose up -d --scale api=3` | ⚠️ Incompatible avec un `container_name` fixe ou un port hôte statique (`"8080:4000"`) : plusieurs conteneurs ne peuvent pas partager le même nom ni réserver le même port hôte |
| Arrêter et nettoyer (garde les volumes) | `docker compose down` | Supprime conteneurs + réseau du projet, **conserve les volumes nommés** |
| Tout supprimer, y compris les volumes | `docker compose down --volumes` (ou `-v`) | Perte de données définitive |
| Utiliser plusieurs fichiers Compose (ex: prod) | `docker compose -f docker-compose.yml -f docker-compose.prod.yml up -d` | Le second fichier vient surcharger/compléter le premier |

⚠️ Ressources créées **manuellement** (`docker network create ...`, `docker volume create ...`) en dehors de Compose ne sont **jamais** nettoyées par `docker compose down`, même avec `--volumes`, même si elles portent le même nom que celles du YAML — il faut les supprimer explicitement :
```bash
docker network rm mon_reseau
docker volume rm mes_donnees
```

---

<a id="12"></a>
## 12. Précédence des variables d'environnement

Du **plus prioritaire** au **moins prioritaire** :

1. **`-e` de `docker run`, ou `environment:` dans le `docker-compose.yml`** — la valeur explicite définie au lancement du conteneur, toujours gagnante.
2. **Fichier `.env` de Compose** — sert uniquement à substituer les `${VARIABLE}` **dans le fichier YAML lui-même** (ex: pour l'image, le port). Si un conflit direct existe avec une valeur posée dans `environment:`, c'est `environment:` qui l'emporte.
3. **`ENV` du Dockerfile** — la moins prioritaire : c'est juste la valeur par défaut intégrée dans l'image, écrasée dès qu'une variable du même nom est fournie au moment du `run` (ou dans `environment:`).

**Exemple concret :** si le Dockerfile a `ENV PORT=3000`, le `.env` de Compose a `PORT=5000`, et le `docker-compose.yml` définit `environment: - PORT=4000`, alors c'est **`PORT=4000`** qui sera utilisé dans le conteneur (le `environment:` explicite gagne toujours).

---

<a id="13"></a>
## 13. Publier une image sur Docker Hub

```bash
docker tag mon-app:1.0 monuser/mon-app:1.0
docker login
docker push monuser/mon-app:1.0
docker pull monuser/mon-app:1.0     # depuis une autre machine
docker images monuser/*             # vérifier ce qui a été récupéré
```
- `docker tag` **ne duplique pas** l'image sur le disque : il crée un alias qui pointe vers les mêmes couches.
- Le format `utilisateur/nom_image:tag` est **obligatoire** pour publier sous ton compte Docker Hub — `docker tag api monuser/shop-api:1.0` échoue si `api` n'est pas le nom exact d'une image existante localement (il faut reprendre le nom donné lors du `docker build -t ...`).
- `docker pull` nécessite que le repository soit public, ou d'être connecté (`docker login`) si l'image est privée.

---

<a id="14"></a>
## 14. Monitoring et nettoyage

```bash
docker stats                    # CPU/RAM/réseau de tous les conteneurs, en direct
docker stats --no-stream web    # une seule mesure instantanée, pas de flux continu
docker top web                  # liste des processus à l'intérieur du conteneur
docker system prune             # nettoyage : conteneurs arrêtés, réseaux et images "dangling" inutilisés, cache de build
docker system prune -a          # + toutes les images non utilisées par un conteneur (plus agressif)
```
`docker system prune` demande toujours une confirmation (`y/N`) avant de supprimer quoi que ce soit.

---

<a id="15"></a>
## 15. Pense-bête — toutes les commandes essentielles

| Catégorie | Commande |
|---|---|
| Lancer un conteneur | `docker run -d --name X -p H:C -e VAR=val --network N -v vol:/chemin image` |
| Lister conteneurs actifs / tous | `docker ps` / `docker ps -a` |
| Logs / logs en direct | `docker logs X` / `docker logs -f X` |
| Terminal dans un conteneur | `docker exec -it X bash` (ou `sh` sur Alpine) |
| Stop / start / restart | `docker stop X` / `docker start X` / `docker restart X` |
| Pause / unpause | `docker pause X` / `docker unpause X` |
| Supprimer un conteneur | `docker rm X` (`docker rm -f X` pour forcer) |
| Copier fichier | `docker cp X:/chemin .` ou `docker cp fichier X:/chemin` |
| Modifier à chaud (RAM) | `docker update --memory="512m" X` |
| Inspecter | `docker inspect X` |
| Lister / supprimer images | `docker images` / `docker rmi image` |
| Construire une image | `docker build -t nom:tag .` |
| Créer / lister / inspecter / supprimer un volume | `docker volume create/ls/inspect/rm` |
| Créer / lister / supprimer un réseau | `docker network create/ls/rm` |
| Tag + publier sur Docker Hub | `docker tag`, `docker login`, `docker push`, `docker pull` |
| Compose : lancer / état / logs | `docker compose up -d` / `ps` / `logs -f` |
| Compose : exec / restart / scale | `docker compose exec X sh` / `restart X` / `up -d --scale X=3` |
| Compose : nettoyer | `docker compose down` (`--volumes` pour tout supprimer) |
| Monitoring | `docker stats`, `docker top X` |
| Nettoyage global | `docker system prune [-a]` |

---

## 🎓 Les 5 pièges les plus fréquents (à ne plus refaire)

1. **Oublier le nom de l'image en fin de `docker run`** → Docker cherche une image portant le nom que tu voulais donner au conteneur.
2. **Confondre `docker restart`/`docker start` avec `docker run`** → on ne peut pas ajouter réseau/volume/mémoire à un conteneur existant sans le recréer (sauf `docker update` pour certains réglages).
3. **YAML de Compose** : toujours au pluriel (`services`, `ports`, `networks`, `volumes`), toujours un espace après `-` dans une liste, jamais d'espace avant `:`.
4. **`docker cp`/`docker rmi`/`docker volume rm`** ne sont **pas** `docker copy`/`docker rm`/`docker rmv` — chaque type d'objet (conteneur, image, volume, réseau) a sa propre sous-commande de suppression/manipulation.
5. **`ARG` disparaît après le build**, `ENV` persiste — si tu veux qu'une valeur soit visible au runtime, il faut la recopier explicitement d'`ARG` vers `ENV`.