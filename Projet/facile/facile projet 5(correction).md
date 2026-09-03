# Correction — Projet 5 : Plateforme de notes (Notes API)

Ce document reprend chaque question, votre réponse, la correction et une mini explication.

---

## Partie 1 — Découverte manuelle (sans Compose)

### Q1 — Lancer le conteneur MySQL
**Votre réponse :**
```bash
docker run -d --name notes_db -e MYSQL_ROOT_PASSWORD=secret123 mysql
```
✅ Correcte dans la syntaxe.
> **Explication :** deux détails à noter : l'énoncé demandait le nom `naotes_db` (vous avez écrit `notes_db`), et il vaut mieux figer une version (`mysql:8`) plutôt que `mysql` (= `latest`), pour éviter que le comportement change si l'image est mise à jour.

### Q2 — Vérifier l'état et les logs
**Votre réponse :**
```bash
docker ps
docker logs notes_db
```
✅ Correcte.
> **Explication :** `docker logs -f notes_db` permettrait de suivre les logs en direct plutôt qu'un simple instantané, utile pour attendre le message `ready for connections`.

### Q3 — Arrêter et supprimer
**Votre réponse :**
```bash
docker rm -f notes_db
```
✅ Correcte et efficace.
> **Explication :** le flag `-f` (force) enchaîne stop + rm en une seule commande, ce qui répond bien à la consigne "arrêtez et supprimez".

---

## Partie 2 — Dockerfile de l'API

### Q4 — server.js et .dockerignore
`server.js` manquant, en voici la version attendue :
```javascript
const http = require('http');
const PORT = 4000;

const server = http.createServer((req, res) => {
  res.writeHead(200, { 'Content-Type': 'text/plain' });
  res.end('API Notes en ligne');
});

server.listen(PORT, () => console.log(`Serveur sur le port ${PORT}`));
```
`.dockerignore` avec `node_modules` : ✅ correct.
> **Explication :** un serveur HTTP minimal en Node.js natif (module `http`) suffit ici, pas besoin de framework.

### Q5 — Dockerfile multi-stage
**Votre réponse :**
```dockerfile
FROM node:18 AS builder
COPY package*.json ./
RUN npm install
COPY . .

FROM node:18-alpine
COPY builder
CMD ["node", "server.js"]
```
⚠️ Le principe (builder → image finale légère) est bon, mais :
- `COPY builder` n'est pas une syntaxe valide.
- Aucun `WORKDIR` défini.

**Version corrigée :**
```dockerfile
FROM node:18 AS builder
WORKDIR /app
COPY package*.json ./
RUN npm install --production
COPY . .

FROM node:18-alpine
WORKDIR /app
RUN addgroup -S appgroup && adduser -S appuser -G appgroup
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/server.js ./server.js
USER appuser
CMD ["node", "server.js"]
```
> **Explication :** `COPY --from=<stage> <source> <destination>` est la syntaxe correcte pour copier des fichiers depuis une étape précédente. `WORKDIR` fixe le dossier de travail (évite d'éparpiller les fichiers à la racine `/`). `USER appuser` fait tourner le process en non-root, ce qui limite les dégâts en cas de faille dans l'application.

### Q6 — ARG et ENV
**Votre réponse :**
```dockerfile
FROM node:18-alpine
ARG NODE_ENV=production
COPY builder
CMD ["node", "server.js"]
```
❌ Erreur importante : un `ARG` seul n'existe que pendant le build, il disparaît dans l'image finale.

**Correction :**
```dockerfile
ARG NODE_ENV=production
ENV NODE_ENV=${NODE_ENV}
```
> **Explication :** `ARG` = variable de build uniquement. `ENV` = variable persistante, visible dans le conteneur en cours d'exécution via `docker inspect` ou `printenv`. Pour "convertir" un ARG en variable d'environnement durable, il faut explicitement faire `ENV X=${X}`.

---

## Partie 3 — Docker Compose

### Q7 — Fichier .env
**Votre réponse :**
```
DB_PASSWORD=secret123
API_PORT=4000
```
✅ Correcte, rien à ajouter.

### Q8 — docker-compose.yml (services db/api)
**Votre réponse :**
```yaml
services:
    api:
        port=${API_PORT}
        depends_on:
            -db
    db:
        environments:
            -MYSQL_ROOT_PASSWORD=${MYSQL_ROOT_PASSWORD}
        restart: unless-stopped
        volumes:
            -notes_db_data
        healthcheck:
            CMD ["mysqladmin", "ping"]
            interval: 10s
```
❌ Plusieurs erreurs :
1. `port=${API_PORT}` → la clé est `ports` (liste `"hôte:conteneur"`).
2. `environments` → la bonne clé est `environment` (singulier).
3. `${MYSQL_ROOT_PASSWORD}` ne correspond à rien dans le `.env` (qui définit `DB_PASSWORD`).
4. `volumes: -notes_db_data` : il manque le point de montage (`:/var/lib/mysql`) et la déclaration du volume nommé au niveau racine du fichier.
5. `healthcheck` : la commande doit être sous une clé `test:`.
6. `depends_on` sans `condition: service_healthy` n'attend pas que MySQL soit prêt, juste que le conteneur démarre.

**Version corrigée :**
```yaml
services:
  db:
    image: mysql:8
    environment:
      MYSQL_ROOT_PASSWORD: ${DB_PASSWORD}
    restart: unless-stopped
    volumes:
      - notes_db_data:/var/lib/mysql
    healthcheck:
      test: ["CMD", "mysqladmin", "ping", "-h", "localhost"]
      interval: 10s
      timeout: 5s
      retries: 5

  api:
    build: ./notes-api
    ports:
      - "${API_PORT}:4000"
    depends_on:
      db:
        condition: service_healthy

volumes:
  notes_db_data:
```
> **Explication :** en YAML, chaque clé Compose a un nom précis (`ports`, `environment`, `volumes`, `healthcheck.test`) — une faute de frappe rend la clé invalide et Compose l'ignore silencieusement ou plante. `condition: service_healthy` fait vraiment attendre que le healthcheck de `db` passe au vert avant de démarrer `api`.

### Q9 — Réseau notes_network
**Votre réponse :**
```yaml
api:
    network:
        -notes_network
db:
    network:
        -notes_network
```
❌ La clé est `networks` (pluriel), et il faut déclarer le réseau au niveau racine.

**Correction :**
```yaml
services:
  api:
    networks:
      - notes_network
  db:
    networks:
      - notes_network

networks:
  notes_network:
```
> **Explication :** sans déclaration explicite au niveau racine (`networks:` en dehors de `services:`), Compose ignore la référence et retombe sur son réseau par défaut.

---

## Partie 4 — Lancement et vérifications

### Q10 — Lancer la stack
**Votre réponse :**
```bash
docker compose up -d
```
✅ Correcte.
> **Explication :** ajoutez `--build` si le Dockerfile a changé depuis la dernière construction de l'image.

### Q11 — État + logs réunis
**Votre réponse :**
```bash
docker logs -f api
docker logs -f db
```
⚠️ Ne répond pas exactement à la consigne (état des services + logs réunis **en une seule commande** pour les logs).

**Correction :**
```bash
docker compose ps
docker compose logs
```
> **Explication :** `docker compose logs` agrège automatiquement les logs de tous les services de la stack, préfixés par leur nom, en une seule commande — contrairement à `docker logs` qui cible un seul conteneur à la fois.

### Q12 — Vérifier l'API avec curl
**Votre réponse :**
```bash
curl http://localhost:4000
```
✅ Correcte.
> **Explication :** on peut aussi écrire `curl http://localhost:${API_PORT}` pour rester dynamique par rapport au `.env`.

### Q13 — Consommation CPU/mémoire en temps réel
**Pas de réponse fournie.**

**Correction :**
```bash
docker stats $(docker compose ps -q)
```
> **Explication :** `docker stats` affiche CPU/RAM/réseau en temps réel ; `docker compose ps -q` liste les IDs des conteneurs du projet, ce qui filtre l'affichage à cette stack uniquement.

---

## Partie 5 — Debug courant

### Q14 — Ouvrir un shell dans le service api
**Votre réponse :**
```bash
docker exect -it api bash
```
⚠️ Coquille (`exect` → `exec`) et `bash` n'existe pas par défaut sur `alpine`.

**Correction :**
```bash
docker compose exec api sh
```
> **Explication :** `docker compose exec` cible un service via son nom dans le fichier Compose (pas besoin de connaître le nom exact du conteneur), et `alpine` ne fournit que `sh`, pas `bash`, sauf installation explicite.

### Q15 — Vérifier que db est joignable
**Pas de réponse fournie.**

**Correction (depuis le shell du service api) :**
```bash
ping db
# ou, plus pertinent pour une base de données :
nc -zv db 3306
```
> **Explication :** Compose crée une résolution DNS automatique entre les services d'un même réseau : le nom du service (`db`) sert directement de hostname.

### Q16 — Redémarrer uniquement api
**Votre réponse :**
```bash
docker restart api
```
⚠️ Fonctionne si le nom du conteneur correspond, mais il est préférable de rester dans l'écosystème Compose.

**Correction :**
```bash
docker compose restart api
```
> **Explication :** cette commande garantit que c'est bien Compose qui gère le cycle de vie du service, sans dépendre du nom réel du conteneur.

---

## Partie 6 — Publication

### Q17 — Taguer l'image
**Votre réponse :**
```bash
docker tag api monuser/notes-api:1.0
```
❌ Le nom source `api` n'existe probablement pas tel quel.

**Correction :**
```bash
docker images   # pour identifier le nom réel, ex: notes-api-api:latest
docker tag notes-api-api:latest monuser/notes-api:1.0
```
> **Explication :** quand Compose build un service, il nomme l'image `<nom_du_dossier_projet>-<nom_du_service>` (ou avec un underscore selon la version de Compose), pas simplement le nom du service.

### Q18 — Se connecter et publier
**Votre réponse :**
```bash
docker login
docker pull
```
❌ `docker login` est correct, mais `docker pull` télécharge une image, ça ne publie rien.

**Correction :**
```bash
docker login
docker push monuser/notes-api:1.0
```
> **Explication :** `docker push` envoie l'image taguée vers le registry (Docker Hub par défaut) ; `docker pull` fait l'inverse (télécharger une image existante).

---

## Partie 7 — Nettoyage et bilan

### Q19 — Nettoyage en conservant le volume
**Pas de réponse fournie.**

**Correction :**
```bash
docker compose down
```
> **Explication :** sans l'option `-v`, cette commande supprime conteneurs et réseau créés par Compose, mais conserve les volumes nommés (dont `notes_db_data`) — exactement ce qui est demandé.

### Q20 — .env et Dockerfile doivent-ils être versionnés ?
**Pas de réponse fournie.**

**Correction :**
- **`.env` → NON.** Il contient un secret (`DB_PASSWORD`). Une fois committé dans Git, il reste visible dans l'historique même après suppression ultérieure. Bonne pratique : l'ajouter à `.gitignore` et fournir un `.env.example` sans valeurs sensibles.
- **`Dockerfile` → OUI.** Il ne contient aucun secret, c'est une recette de build au même titre que du code source. Le versionner garantit la reproductibilité du build (même image reconstruite par toute l'équipe ou en CI/CD) et permet de suivre son historique de modifications.

---

## Bilan global

| Compétence | Statut |
|---|---|
| Commandes Docker de base (run, logs, rm) | ✅ Maîtrisé |
| Dockerfile multi-stage | ⚠️ Syntaxe `COPY --from` à revoir |
| ARG vs ENV | ❌ À revoir (ARG ne persiste pas seul) |
| Syntaxe YAML Compose (ports, environment, volumes, healthcheck) | ❌ Plusieurs erreurs de clés à corriger |
| depends_on avec condition service_healthy | ❌ Non utilisé |
| Réseaux Compose | ❌ Clé `networks` mal orthographiée/déclarée |
| Commandes Compose (up, ps, logs, exec, restart) | ⚠️ Bonnes idées mais parfois `docker` au lieu de `docker compose` |
| Publication sur un registry (tag/push) | ❌ Confusion push/pull |
| Bonnes pratiques Git (.env vs Dockerfile) | Non traité |

**Point à retravailler en priorité :** la syntaxe YAML de Compose (clés exactes : `ports`, `environment`, `networks`, `healthcheck.test`) et la distinction `ARG`/`ENV` dans le Dockerfile.