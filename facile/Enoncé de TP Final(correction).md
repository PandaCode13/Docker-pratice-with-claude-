# 📋 Correction annotée — TP Final Docker de A à Z

Pour chaque question : **ta réponse**, **la correction**, et une **mini-explication**.

---

## Partie 1 — Bases

### Question 1

**Ta réponse :**
```bash
docker run nginx
```

**Correction :**
```bash
docker run -d nginx
```

**Explication :** Il manque `-d` (mode détaché). Sans lui, le conteneur tourne au premier plan et bloque ton terminal (tu ne récupères la main qu'en faisant `Ctrl+C`, ce qui arrête aussi le conteneur).

---

### Question 2

**Ta réponse :**
```bash
docker run shop_web -p 8080:80
```

**Correction :**
```bash
docker run -d --name shop_web -p 8080:80 nginx
```

**Explication :** Trois problèmes :
- `shop_web` est interprété comme le **nom de l'image** à lancer (il manque `--name` devant), Docker chercherait une image appelée `shop_web` et échouerait.
- Il manque `-d` pour le mode détaché.
- Il manque le **nom de l'image** (`nginx`) à la fin de la commande.

---

### Question 3

**Ta réponse :**
```bash
docker ps
```

**Correction :** ✅ Correcte.

---

### Question 4

**Ta réponse :**
```bash
docker rm -f shop_web
```

**Correction :** Fonctionne, mais la consigne demandait deux actions distinctes ("arrêtez **puis** supprimez") :
```bash
docker stop shop_web
docker rm shop_web
```

**Explication :** `docker rm -f` fait bien les deux (stop forcé + suppression) en une commande, donc le résultat est correct — mais si l'exercice attend deux étapes séparées pour vérifier que tu maîtrises `stop` et `rm` indépendamment, mieux vaut les écrire l'une après l'autre.

---

## Partie 2 — Logs, exec, images

### Question 5

**Ta réponse :**
```bash
docker run -d\
--name shop_db\
-e MYSQL_ROOT_PASSWORD=secret123\
mysql 
```

**Correction :**
```bash
docker run -d \
  --name shop_db \
  -e MYSQL_ROOT_PASSWORD=secret123 \
  mysql
```

**Explication :** Détail important sur le `\` de fin de ligne en bash : il faut un **espace avant** le backslash. En bash, `\` en fin de ligne supprime le saut de ligne et **colle** littéralement la ligne suivante à la précédente. Ton `-d\` suivi de `--name` sur la ligne d'après donnerait, une fois les lignes fusionnées : `-d--name shop_db...` (un seul bloc invalide), au lieu de `-d --name shop_db...`. Toujours écrire `-d \` (avec un espace) avant le retour à la ligne.

---

### Question 6

**Ta réponse :**
```bash
docker logs shop_db
```

**Correction :**
```bash
docker logs shop_db
docker logs -f shop_db
```

**Explication :** La question demandait deux choses : afficher les logs, **puis** les suivre en direct. Il manque la deuxième commande avec `-f` (follow).

---

### Question 7

**Ta réponse :**
```bash
docker exec -it shop_db bash
```

**Correction :** ✅ Correcte.

---

### Question 8

**Ta réponse :**
```bash
docker ps
```

**Correction :**
```bash
docker images
```

**Explication :** La question 8 demande de lister les **images** locales, pas les conteneurs en cours d'exécution (ça, c'est `docker ps`, déjà fait en question 3). `docker images` liste les images téléchargées/construites sur ta machine.

---

## Partie 3 — Cycle de vie, ressources, transfert

### Question 9

**Ta réponse :**
```bash
docker inspect shop_db
```

**Correction :** ✅ Correcte.

---

### Question 10

**Ta réponse :**
```bash
docker restart shop_db
```

**Correction :** ✅ Correcte.

---

### Question 11

**Ta réponse :**
```bash
docker pause shop_db 
docker unpause shop_db
```

**Correction :** ✅ Correcte.

---

### Question 12

**Ta réponse :**
```bash
docker copy dump.sql shop_db:/tmp/dump.sql
```

**Correction :**
```bash
docker cp dump.sql shop_db:/tmp/dump.sql
```

**Explication :** La commande s'appelle `docker cp`, pas `docker copy` (qui n'existe pas). Sinon la syntaxe (source locale → `conteneur:chemin`) est correcte.

---

### Question 13

**Ta réponse :**
```bash
docker run -d --memory=512m shop_db
```

**Correction :**
```bash
docker stop shop_db
docker rm shop_db
docker run -d --name shop_db \
  -e MYSQL_ROOT_PASSWORD=secret123 \
  --memory="512m" \
  mysql
```
ou, sans recréer le conteneur :
```bash
docker update --memory="512m" --memory-swap="512m" shop_db
```

**Explication :** `shop_db` est ici traité comme un **nom d'image** (pas un conteneur existant) puisqu'on est sur `docker run` : Docker chercherait à récupérer une image `shop_db` (qui n'existe pas) et échouerait. Pour "relancer" un conteneur existant avec une nouvelle contrainte, il faut soit le supprimer et le recréer avec `docker run --name shop_db ... --memory`, soit utiliser `docker update` qui modifie la limite à chaud sans suppression. Il manquait aussi `--name` et `-e MYSQL_ROOT_PASSWORD=...` dans ta version.

---

### Question 14

**Ta réponse :**
```bash
docker volume create shop_db_data
docker network create shop_network
```

**Correction :** ✅ Correcte.

---

### Question 15

**Ta réponse :**
```bash
docker restart shop_db -n shop_network -v shop_db_data:/var/lib/mysql
```

**Correction :**
```bash
docker stop shop_db
docker rm shop_db
docker run -d --name shop_db \
  --network shop_network \
  -v shop_db_data:/var/lib/mysql \
  -e MYSQL_ROOT_PASSWORD=secret123 \
  mysql
```

**Explication :** `docker restart` ne fait que redémarrer un conteneur **tel qu'il a été créé** : il n'accepte **aucun** flag comme `-n` (qui n'existe d'ailleurs pas comme alias de réseau) ou `-v`. On ne peut pas attacher un réseau ou un volume à un conteneur déjà existant via `restart`. La seule solution est de le supprimer et de le relancer avec `docker run` en incluant ces options dès le départ.

---

### Question 16

**Ta réponse :**
```bash
docker inspect -v shop_db_data
```

**Correction :**
```bash
docker volume inspect shop_db_data
```

**Explication :** `-v` n'est pas un flag valide de `docker inspect` (les options existantes sont `-f`/`--format`, `-s`/`--size`, `--type`). Pour inspecter un volume, la commande dédiée `docker volume inspect` est la plus claire — elle retourne directement le JSON du volume, avec le champ `"Mountpoint"` indiquant son emplacement réel sur le disque hôte.

---

## Partie 5 — Dockerfile de base

### Question 17

**Ta réponse :**
```dockerfile
FROM node:18
WORKDIR /app
COPY package*.json ./
COPY . . 
EXPOSE 4000
CMD ["node", "server.js"]
```

**Correction :** ✅ Correcte — et même plutôt bien pensée : séparer `COPY package*.json ./` de `COPY . .` est une bonne pratique (permet à Docker de mettre en cache la couche `npm install` si seul le code change, pas les dépendances), même si l'énoncé ne le demandait pas explicitement à cette étape.

---

### Question 18

**Ta réponse :** *(vide)*

**Correction :**
```bash
docker build -t shop-api:1.0 .
```

**Explication :** Sans cette étape, l'image `shop-api:1.0` n'existe pas — indispensable pour la suite du TP (Partie 7 et 9 en dépendent).

---

## Partie 6 — Bonnes pratiques Dockerfile

### Question 19

**Ta réponse :**
```dockerfile
FROM node:18 AS builder
WORKDIR /app
COPY package*.json ./
COPY . . 
EXPOSE 4000
CMD ["node", "server.js"]
```

**Correction :**
```dockerfile
# --- Étape 1 : builder ---
FROM node:18 AS builder
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .

# --- Étape 2 : image finale allégée ---
FROM node:18-alpine
WORKDIR /app
COPY --from=builder /app .
EXPOSE 4000
CMD ["node", "server.js"]
```

**Explication :** Deux problèmes importants :
1. **`RUN npm install` manquant** : tu copies `package*.json` mais tu n'installes jamais les dépendances. Sans ce `RUN`, le dossier `node_modules` n'existera jamais dans l'image.
2. **Ce n'est pas un multi-stage build** : il n'y a qu'un seul `FROM`. Un multi-stage nécessite **deux** étapes : une étape `builder` (ici `node:18`, qui installe et prépare le code) et une étape finale distincte (`node:18-alpine`) qui **récupère uniquement le résultat** via `COPY --from=builder`. `EXPOSE` et `CMD` n'ont de sens que dans l'image finale exécutée, pas dans l'étape de build intermédiaire (qui n'est jamais lancée telle quelle).

---

### Question 20

**Ta réponse :**
```
node_modules 
.env
```

**Correction :** ✅ Correcte.

---

### Question 21

**Ta réponse :** *(vide)*

**Correction :**
```dockerfile
RUN addgroup -S appgroup && adduser -S appuser -G appgroup
USER appuser
```

**Explication :** Sur une image Alpine (BusyBox), on crée un groupe et un utilisateur système avec `addgroup -S` / `adduser -S`, puis on bascule dessus avec `USER`. Exécuter le conteneur en `root` par défaut est un risque de sécurité inutile en cas de compromission de l'application.

---

### Question 22

**Ta réponse :** *(vide)*

**Correction :**
```dockerfile
ARG NODE_ENV=production
ENV NODE_ENV=$NODE_ENV
```

**Explication :** Un `ARG` n'existe **qu'au moment du build** et disparaît ensuite — il n'est pas visible dans le conteneur au runtime (ex: `process.env.NODE_ENV` serait `undefined`). Pour le "persister" dans l'image finale, il faut le réinjecter dans une variable `ENV`.

---

## Partie 7 — Docker Compose

### Question 23 & 24

**Ta réponse :**
```yaml
services : 
    api: 
        build:
        network:
            -shop_network
        depends_on: 
            - db
            condition: service_healthy
    
    db:
        image: MySQL
        network:
            -shop_network
        volume:
        restart: unless-stopped
        healthcheck: 
            cmd: "mysqladmin ping"

network:
    shop_network
```

**Correction :**
```yaml
services:
  api:
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - "${API_PORT}:4000"
    depends_on:
      db:
        condition: service_healthy
    networks:
      - shop_network

  db:
    image: mysql
    environment:
      MYSQL_ROOT_PASSWORD: ${DB_PASSWORD}
    volumes:
      - shop_db_data:/var/lib/mysql
    restart: unless-stopped
    healthcheck:
      test: ["CMD", "mysqladmin", "ping", "-h", "localhost"]
      interval: 10s
      timeout: 5s
      retries: 5
    networks:
      - shop_network

volumes:
  shop_db_data:

networks:
  shop_network:
    name: shop_network
```

**Explication — il y a beaucoup d'erreurs de syntaxe YAML à corriger :**
- `services :` → pas d'espace avant les deux-points en YAML : `services:`.
- `network:` (singulier) **n'existe pas** en Compose, ni au niveau d'un service ni au niveau racine. La clé correcte est `networks:` (pluriel), à la fois dans chaque service et à la racine du fichier.
- `-shop_network` sans espace après le tiret n'est pas reconnu comme un élément de liste YAML valide — il faut **impérativement un espace** après le `-` : `- shop_network`.
- `build:` sans valeur est incomplet : il faut préciser au minimum un chemin (`build: .`) ou un objet avec `context:`/`dockerfile:`.
- `depends_on:` mélange une syntaxe **liste** (`- db`) et une syntaxe **mapping** (`condition: service_healthy`) sous la même clé — c'est invalide en YAML. Pour utiliser `condition:`, il faut abandonner le format liste et écrire un mapping : `db:` puis `condition: service_healthy` en dessous, indenté.
- `image: MySQL` : les noms d'images Docker Hub doivent être en **minuscules** (`mysql`), sinon Docker refuse la référence (*"repository name must be lowercase"*).
- `volume:` (singulier) est incorrect, la clé est `volumes:` (pluriel), avec une liste `- nom_volume:/chemin`.
- Il manque totalement la variable `MYSQL_ROOT_PASSWORD` dans le service `db` (pourtant demandée en Q25).
- Il manque le port publié (`ports:`) pour le service `api`.
- Le `healthcheck` utilise `cmd:`, qui n'est pas une clé reconnue — la bonne clé est `test:` (avec une liste au format `["CMD", ...]` ou une chaîne `CMD-SHELL ...`), accompagnée idéalement de `interval`, `timeout`, `retries`.
- Il manque la déclaration du volume `shop_db_data` au niveau racine (`volumes:`).

---

### Question 25

**Ta réponse :**
```
DB_PASSWORD=
API_PORT=
```

**Correction :**
```
DB_PASSWORD=secret123
API_PORT=8080
```

**Explication :** Les clés sont bien nommées, mais les valeurs sont vides — sans valeur, `${DB_PASSWORD}` sera substitué par une chaîne vide dans le `docker-compose.yml`, ce qui ferait planter MySQL (mot de passe root vide non autorisé par défaut) et empêcherait l'API de publier un port cohérent.

---

### Question 26

**Ta réponse :**
```bash
docker compose up -d
```

**Correction :** ✅ Correcte.

---

## Partie 8 — Exploitation de la stack

### Question 27

**Ta réponse :**
```bash
docker compose
```

**Correction :**
```bash
docker compose ps
```

**Explication :** `docker compose` seul n'affiche que l'aide de la commande. Il manque la sous-commande `ps`, qui liste l'état des services du projet.

---

### Question 28

**Ta réponse :**
```bash
docker logs
```

**Correction :**
```bash
docker compose logs
```

**Explication :** `docker logs` (sans `compose`) est la commande Docker "classique" qui exige un nom de conteneur précis en argument — elle ne peut pas afficher les logs de **tous** les services d'un coup. `docker compose logs` récupère automatiquement les logs de tous les conteneurs du projet.

---

### Question 29

**Ta réponse :**
```bash
docker compose exec -it api
```

**Correction :**
```bash
docker compose exec api sh
```

**Explication :** Il manque la **commande à exécuter** à la fin (`sh` ou `bash`) — sans elle, Docker ne sait pas quoi lancer dans le conteneur. Attention aussi : l'image finale du multi-stage est `node:18-alpine`, qui ne fournit pas `bash` par défaut, seulement `sh`.

---

### Question 30

**Ta réponse :**
```bash
docker restart api
```

**Correction :**
```bash
docker compose restart api
```

**Explication :** `docker restart api` cherche un conteneur nommé littéralement `api`, ce qui ne correspond pas forcément au nom réel généré par Compose (souvent préfixé, ex: `projet-api-1`), et n'est pas l'outil "Compose-natif" attendu ici. `docker compose restart api` cible le bon conteneur en se basant sur le nom du service défini dans le YAML, peu importe le nom réel du conteneur.

---

### Question 31

**Ta réponse :**
```bash
docker
```

**Correction :**
```bash
docker stats
```
ou, pour se limiter aux conteneurs de la stack :
```bash
docker stats $(docker compose ps -q)
```

**Explication :** `docker` seul n'affiche que l'aide générale. `docker stats` affiche en temps réel la consommation CPU/mémoire/réseau de tous les conteneurs actifs.

---

### Question 32

**Ta réponse :**
```bash
docker run --scale 3 api
```

**Correction :**
```bash
docker compose up -d --scale api=3
```

**Explication :** `--scale` n'est **pas** une option de `docker run` (qui lance un conteneur unique) — c'est une option de `docker compose up`, qui gère plusieurs conteneurs d'un même service. La syntaxe est aussi différente : `--scale api=3` (nom_du_service=nombre_d'instances), pas `--scale 3 api`.

---

## Partie 9 — Registre Docker Hub

### Question 33

**Ta réponse :**
```bash
docker tag api monuser/shop-api:1.0
```

**Correction :**
```bash
docker tag shop-api:1.0 monuser/shop-api:1.0
```

**Explication :** L'image locale construite en Q18 s'appelle `shop-api:1.0`, pas `api` (qui n'existe pas en tant qu'image). Le premier argument de `docker tag` doit être le nom **exact** de l'image source existante.

---

### Question 34

**Ta réponse :**
```bash
docker login
```

**Correction :** ✅ Correcte.

---

### Question 35

**Ta réponse :** *(vide)*

**Correction :**
```bash
docker push monuser/shop-api:1.0
```

---

### Question 36

**Ta réponse :** *(vide)*

**Correction :**
```bash
docker pull monuser/shop-api:1.0
```

**Explication :** Depuis une machine "vide" (sans l'image en local), `docker pull` télécharge l'image depuis Docker Hub — nécessite que le repository soit public, ou d'être connecté (`docker login`) si l'image est privée.

---

## Partie 10 — Nettoyage et réflexion finale

*(Cette partie n'a pas été traitée dans ta copie — voici les réponses attendues.)*

### Question 37

**Correction :**
```bash
docker compose down
```

**Explication :** Par défaut, `docker compose down` supprime les conteneurs et le réseau du projet mais **conserve les volumes nommés**.

---

### Question 38

**Correction :**
```bash
docker compose down --volumes
```

**Explication :** Ajouter `--volumes` (ou `-v`) supprime en plus les volumes déclarés dans le `docker-compose.yml` — perte de données irréversible.

---

### Question 39

**Correction :**
```bash
docker network rm shop_network
docker volume rm shop_db_data
```

**Explication :** Ces ressources ont été créées **manuellement** en Partie 4, en dehors de Compose. `docker compose down` (même avec `--volumes`) ne gère que les ressources que Compose a lui-même créées pour son propre projet — les ressources manuelles portant le même nom doivent être supprimées explicitement.

---

### Question 40

**Correction :**
- **`.env` → NON**, à ne pas versionner. Il contient des valeurs sensibles (ici `DB_PASSWORD`). Le versionner exposerait ce secret à quiconque a accès au dépôt. Bonne pratique : l'ajouter au `.gitignore` et fournir un `.env.example` sans valeurs réelles.
- **`Dockerfile` → OUI**, à versionner. Il ne contient pas de secret (s'il est bien écrit) et décrit la logique de build de l'application — indispensable pour la reproductibilité et le travail en équipe.

---

## 🎯 Bilan

| # | Statut |
|---|---|
| Q1, Q3, Q7, Q9, Q10, Q11, Q14, Q17, Q20, Q26, Q34 | ✅ Bonnes |
| Q2, Q4, Q5, Q6, Q8, Q12, Q13, Q15, Q16, Q19, Q23/24, Q25, Q27–32, Q33 | ⚠️ Erreurs de syntaxe, de commande ou incomplètes |
| Q18, Q21, Q22, Q35, Q36 | ❌ Vides |
| Partie 10 (Q37–40) | ❌ Non traitée |

Points à revoir en priorité :
1. **La syntaxe YAML de Compose** (`services`/`networks`/`volumes` au pluriel, indentation des listes, format `depends_on` avec `condition`).
2. **`docker run` vs `docker restart`/`docker update`** : on ne peut pas ajouter un réseau/volume/limite mémoire à un conteneur existant simplement en le "relançant" — il faut soit le recréer, soit utiliser `docker update` quand c'est possible.
3. **Les équivalents "Compose" des commandes Docker classiques** (`docker compose logs/restart/exec` vs `docker logs/restart/exec`), qui ciblent le bon conteneur même si son nom réel diffère du nom du service.