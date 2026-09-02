# 🐳 Exercices Docker — Corrections

Ce document contient une série d'exercices pratiques sur **Docker** et **Docker Compose**, accompagnés de leurs corrections et explications.

L'objectif est de pratiquer les notions essentielles :

* Variables d'environnement
* `.dockerignore`
* Dockerfile
* Multi-stage build
* Utilisateur non-root
* Docker Compose
* Réseaux Docker
* Volumes
* Healthchecks
* Gestion des conteneurs
* Logs
* Statistiques
* Docker Hub
* Nettoyage des ressources

---

## 📋 Sommaire

* [Partie 1 — Variables d'environnement](#partie-1--variables-denvironnement)
* [Partie 2 — Dockerfile](#partie-2--dockerfile)
* [Partie 3 — Docker Compose et réseau](#partie-3--docker-compose-et-réseau)
* [Partie 4 — Gestion et surveillance](#partie-4--gestion-et-surveillance)
* [Partie 5 — Manipulation des conteneurs](#partie-5--manipulation-des-conteneurs)
* [Partie 6 — Arrêt, suppression et sécurité](#partie-6--arrêt-suppression-et-sécurité)

---

# Partie 1 — Variables d'environnement

## Question 1

Dans le dossier `/todo-app` :

```text
.env
DB_PASSWORD=secret123
APP_PORT=5000
```

`.dockerignore` :

```text
node_modules
.env
```

### ✅ Correction

**Correct, rien à changer.**

### 💡 Explication

Le fichier `.env` permet de centraliser les valeurs sensibles ou paramétrables.

Le fichier `.dockerignore` permet d'éviter d'envoyer certains fichiers dans le contexte de build Docker, notamment :

* `node_modules`, qui peut être incompatible avec l'OS du conteneur ;
* `.env`, qui peut contenir des informations sensibles.

---

# Partie 2 — Dockerfile

## Question 2

### ❌ Réponse initiale

```dockerfile
<!-- Stage 1 -->
FROM node:18 AS builder
COPY package*.json ./
RUN npm install
COPY . .

<!-- Stage 2 -->
FROM node:18-alpine
COPY builder
ENTRYPOINT ["node"]
CMD ["server.js"]
```

### ✅ Correction

```dockerfile
# Stage 1 : builder
FROM node:18 AS builder

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

# Stage 2 : image finale
FROM node:18-alpine

WORKDIR /app

COPY --from=builder /app /app

USER node

ENTRYPOINT ["node"]

CMD ["server.js"]
```

### 💡 Explication

Plusieurs erreurs étaient présentes.

#### 1. Commentaires Dockerfile

La syntaxe :

```dockerfile
<!-- commentaire -->
```

correspond à HTML/XML.

Dans un Dockerfile, il faut utiliser :

```dockerfile
# commentaire
```

#### 2. `WORKDIR`

Il manquait :

```dockerfile
WORKDIR /app
```

dans les deux étapes.

Cela permet de travailler dans un dossier dédié plutôt qu'à la racine `/`.

#### 3. `COPY --from`

La commande :

```dockerfile
COPY builder
```

n'est pas valide pour récupérer les fichiers d'une étape précédente.

Il faut utiliser :

```dockerfile
COPY --from=builder /app /app
```

#### 4. Utilisateur non-root

Il manquait également :

```dockerfile
USER node
```

afin que le conteneur final fonctionne avec un utilisateur non-root.

---

## Question 3

### ✅ Correction

```dockerfile
FROM node:18-alpine

ARG NODE_ENV=production

ENV NODE_ENV=${NODE_ENV}

WORKDIR /app

COPY --from=builder /app /app

USER node

ENTRYPOINT ["node"]

CMD ["server.js"]
```

### 💡 Explication

`ARG` permet de déclarer une variable disponible **uniquement pendant le build**.

Par exemple :

```bash
docker build --build-arg NODE_ENV=production .
```

En la recopiant dans :

```dockerfile
ENV NODE_ENV=${NODE_ENV}
```

sa valeur devient disponible dans l'image et dans le conteneur lors de son exécution.

---

# Partie 3 — Docker Compose et réseau

## Question 4 — Question 5

### ❌ Réponse initiale

La configuration contenait plusieurs erreurs de syntaxe et de structure.

### ✅ Correction

```yaml
version: "3.8"

services:

  app:
    build: .
    ports:
      - "${APP_PORT}:5000"
    depends_on:
      db:
        condition: service_healthy
    networks:
      - todo_network

  db:
    image: mysql
    restart: unless-stopped
    environment:
      - MYSQL_ROOT_PASSWORD=${DB_PASSWORD}
    volumes:
      - todo_db_data:/var/lib/mysql
    healthcheck:
      test: ["CMD", "mysqladmin", "ping"]
      interval: 10s
    networks:
      - todo_network

volumes:
  todo_db_data:

networks:
  todo_network:
```

### 💡 Explication

#### `services`

La déclaration doit être :

```yaml
services:
```

et non :

```yaml
services :
```

#### `build`

Le service `app` doit posséder :

```yaml
build: .
```

afin que Docker Compose construise l'image à partir du Dockerfile.

#### `ports`

Il faut utiliser `ports` au pluriel :

```yaml
ports:
  - "${APP_PORT}:5000"
```

Cela permet de faire correspondre :

```text
Port de l'hôte → Port du conteneur
5000           → 5000
```

#### `depends_on`

La bonne syntaxe pour attendre que MySQL soit healthy est :

```yaml
depends_on:
  db:
    condition: service_healthy
```

#### `image`

Le service `db` doit indiquer l'image utilisée :

```yaml
image: mysql
```

#### `environment`

Il faut utiliser :

```yaml
environment:
```

et non :

```yaml
environments:
```

#### Variable d'environnement

La variable initialement utilisée était incorrecte :

```text
MYSQL_ROOT_PASWWORD
```

Elle contenait une faute de frappe et ne correspondait pas au `.env`.

La bonne variable est :

```yaml
MYSQL_ROOT_PASSWORD=${DB_PASSWORD}
```

#### Volume

La déclaration correcte est :

```yaml
volumes:
  - todo_db_data:/var/lib/mysql
```

#### Redémarrage

La configuration :

```yaml
condition: unless-stopped
```

est incorrecte.

Il faut :

```yaml
restart: unless-stopped
```

#### Healthcheck

La commande doit être placée dans `test` :

```yaml
healthcheck:
  test: ["CMD", "mysqladmin", "ping"]
  interval: 10s
```

#### Réseau

Il faut utiliser :

```yaml
networks:
```

et chaque service doit être connecté au réseau :

```yaml
networks:
  - todo_network
```

---

# Partie 4 — Gestion et surveillance

## Question 6

### Réponse

```bash
docker compose up -d
```

### ✅ Correction

**Correct, rien à changer.**

### 💡 Explication

Cette commande démarre les services définis dans le fichier `docker-compose.yml` en arrière-plan.

L'option :

```text
-d
```

signifie **detached mode**.

---

## Question 7

### ❌ Réponse initiale

```bash
docker compose logs app
docker compose logs db
```

### ✅ Correction

```bash
docker compose ps
```

### 💡 Explication

La question demandait de vérifier **l'état des services**.

`docker compose ps` permet notamment de voir si les conteneurs sont :

* en cours d'exécution ;
* arrêtés ;
* healthy ;
* en erreur.

---

## Question 8

### ❌ Réponse initiale

```bash
docker logs
```

### ✅ Correction

```bash
docker compose logs
```

### 💡 Explication

`docker logs` nécessite normalement le nom d'un conteneur.

Pour afficher les logs de tous les services définis dans Docker Compose, on utilise :

```bash
docker compose logs
```

---

## Question 9

### Réponse

```bash
curl http://localhost:5000
```

### ✅ Correction

**Correct, rien à changer.**

### 💡 Explication

Le port `5000` correspond à la valeur définie dans le fichier `.env` :

```text
APP_PORT=5000
```

---

## Question 10

### ✅ Correction

```bash
docker stats
```

### 💡 Explication

Cette commande affiche en temps réel les statistiques des conteneurs actifs, notamment :

* utilisation CPU ;
* utilisation mémoire ;
* consommation réseau ;
* consommation disque.

---

# Partie 5 — Manipulation des conteneurs

## Question 11

### ❌ Réponse initiale

```bash
docker exec -it app bash
```

### ✅ Correction

```bash
docker compose exec app sh
```

### 💡 Explication

La question demandait explicitement l'utilisation de `docker compose`.

De plus, l'image finale utilise :

```dockerfile
FROM node:18-alpine
```

Alpine Linux ne possède généralement pas `bash` par défaut.

Il faut donc utiliser :

```bash
sh
```

---

## Question 12

### ❌ Réponse initiale

```bash
docker restart app
```

### ✅ Correction

```bash
docker compose restart app
```

### 💡 Explication

Docker Compose peut générer un nom de conteneur différent du simple nom du service.

Par exemple :

```text
todo-app-app-1
```

Avec :

```bash
docker compose restart app
```

on cible directement le **service `app`**, sans avoir besoin de connaître le nom réel du conteneur.

---

## Question 13

### ❌ Réponse initiale

```bash
docker tag app monuser/todo-app:1.0
```

### ✅ Correction

```bash
docker tag todo-app-app monuser/todo-app:1.0
```

> Vérifier le nom exact de l'image avec `docker images`, car celui-ci peut varier selon la version et la configuration de Docker Compose.

### 💡 Explication

Docker Compose ne construit pas forcément une image appelée simplement :

```text
app
```

Le nom généré peut être basé sur :

```text
<nom_du_projet>-<nom_du_service>
```

La commande `docker tag` doit utiliser le nom réel de l'image source.

---

## Question 14

### ❌ Réponse initiale

```bash
docker login
docker push app
```

### ✅ Correction

```bash
docker login

docker push monuser/todo-app:1.0
```

### 💡 Explication

La commande :

```bash
docker login
```

permet de se connecter à Docker Hub.

Ensuite, l'image doit être envoyée avec son nom complet :

```text
utilisateur/nom-image:tag
```

Dans notre cas :

```text
monuser/todo-app:1.0
```

---

# Partie 6 — Arrêt, suppression et sécurité

## Question 15

### ❌ Réponse initiale

```bash
docker rm -f app
docker rm -f db
```

### ✅ Correction

```bash
docker compose down
```

### 💡 Explication

`docker compose down` permet de supprimer les conteneurs et le réseau créé par Docker Compose.

Contrairement à :

```bash
docker rm -f
```

la commande Compose gère directement les ressources liées à la stack.

Les volumes nommés sont conservés par défaut.

---

## Question 16

### ✅ Correction

```bash
docker compose down -v
```

### ⚠️ Attention

L'option `-v` supprime également les **volumes nommés**.

Dans notre exemple :

```text
todo_db_data
```

sera supprimé.

Cela signifie que les données stockées dans la base de données seront perdues.

---

## Question 17

### ❌ Réponse

Le fichier `.env` **ne doit pas être versionné dans Git**.

### 💡 Explication

Le fichier `.env` contient notamment :

```text
DB_PASSWORD=secret123
```

Il contient donc une information sensible.

Il faut l'ajouter au `.gitignore` :

```gitignore
.env
```

À la place, on peut créer :

```text
.env.example
```

avec des valeurs fictives :

```env
DB_PASSWORD=your_password_here
APP_PORT=5000
```

Cela permet aux autres développeurs de connaître les variables nécessaires sans exposer les véritables secrets.

---

# 📚 Commandes Docker importantes à retenir

| Besoin                      | Commande                            |
| --------------------------- | ----------------------------------- |
| Démarrer les services       | `docker compose up -d`              |
| Voir les services           | `docker compose ps`                 |
| Voir les logs               | `docker compose logs`               |
| Voir les logs d'un service  | `docker compose logs app`           |
| Voir les statistiques       | `docker stats`                      |
| Entrer dans un conteneur    | `docker compose exec app sh`        |
| Redémarrer un service       | `docker compose restart app`        |
| Arrêter la stack            | `docker compose down`               |
| Supprimer aussi les volumes | `docker compose down -v`            |
| Se connecter à Docker Hub   | `docker login`                      |
| Envoyer une image           | `docker push utilisateur/image:tag` |

---

# 🎯 Objectif de l'exercice

À la fin de cet exercice, vous devez être capable de :

* créer un Dockerfile fonctionnel ;
* utiliser un **multi-stage build** ;
* construire une application Node.js avec Docker ;
* utiliser Docker Compose ;
* connecter plusieurs services avec un réseau Docker ;
* utiliser des volumes persistants ;
* configurer un healthcheck ;
* gérer les variables d'environnement ;
* surveiller les conteneurs ;
* manipuler les conteneurs avec Docker Compose ;
* publier une image sur Docker Hub ;
* supprimer correctement une stack Docker ;
* protéger les informations sensibles.

---

## 🔐 Bonnes pratiques à retenir

> **Ne jamais versionner les secrets.**

Utiliser :

```text
.env
```

pour les valeurs sensibles et :

```text
.env.example
```

pour documenter les variables nécessaires.

Utiliser également :

```dockerfile
USER node
```

afin d'éviter d'exécuter inutilement l'application en tant que `root`.

---
