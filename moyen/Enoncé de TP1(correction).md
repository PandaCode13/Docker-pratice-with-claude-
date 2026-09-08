# Correction — TP 10 (niveau moyen) : Images avancées, ressources, sécurité et diagnostic

Ce document reprend chaque exercice, votre réponse, la correction et une mini explication.

---

### Exercice 1 — Créer une image à partir d'un conteneur modifié

**Votre réponse :**
```bash
docker run ubuntu
curl

docker image create ubuntu-curl:1.0 from ubuntu
```
❌ Plusieurs erreurs :
- `docker run ubuntu` sans commande interactive : le conteneur ubuntu de base n'a pas de processus qui tourne en continu, il démarre puis s'arrête immédiatement.
- `curl` seul dans votre terminal l'exécute sur **votre machine**, pas dans le conteneur.
- `docker image create ... from ...` n'est pas une commande Docker valide.

**Version corrigée :**
```bash
docker run -it --name temp_container ubuntu bash
# --- dans le conteneur ---
apt update && apt install -y curl
exit
# --- de retour sur votre machine ---
docker commit temp_container ubuntu-curl:1.0
```
> **Mini explication :** `-it` ouvre un terminal interactif pour garder le conteneur actif et pouvoir taper des commandes dedans. `docker commit <conteneur> <image:tag>` est la commande qui transforme l'état actuel d'un conteneur (fichiers installés, modifications) en une nouvelle image réutilisable — c'est l'ancêtre historique du Dockerfile, à connaître mais à éviter en production (non reproductible, pas versionné).

---

### Exercice 2 — Voir les modifications d'un conteneur

**Votre réponse :**
```bash
docker diff ubuntu
```
❌ `docker diff` s'applique à un **conteneur**, pas à une image. `ubuntu` ici est le nom de l'image, pas d'un conteneur en cours ou arrêté.

**Version corrigée :**
```bash
docker diff temp_container
```
> **Mini explication :** `docker diff` compare le filesystem actuel du conteneur à celui de l'image d'origine, et préfixe chaque ligne par `A` (ajouté), `C` (modifié) ou `D` (supprimé). C'est utile pour comprendre ce qu'un `commit` va réellement capturer.

---

### Exercice 3 — Exporter une image en fichier

**Votre réponse :**
```bash
docker export ubuntu-curl:1.0 ubuntu-curl.tar
```
❌ Deux erreurs :
- `docker export` s'applique à un **conteneur**, pas à une image — il exporte le filesystem à plat, sans l'historique des couches ni les métadonnées (CMD, ENV, etc.).
- La syntaxe elle-même est fausse : `docker export` ne prend pas un fichier de sortie en second argument, il faut `-o` ou une redirection.

**Version corrigée :**
```bash
docker save -o ubuntu-curl.tar ubuntu-curl:1.0
```
> **Mini explication :** pour une **image**, la commande à utiliser est `docker save` (et son inverse `docker load`, que vous avez bien utilisé à l'exercice 4). `docker export`/`docker import` sont réservés aux **conteneurs** et ne conservent pas l'historique des couches ni les instructions du Dockerfile — seulement le filesystem final.

---

### Exercice 4 — Importer une image depuis un fichier

**Votre réponse :**
```bash
docker load -i ubuntu-curl.tar
```
✅ Correcte.
> **Mini explication :** `docker load` est bien le pendant de `docker save`, et recharge l'image avec toutes ses couches et métadonnées d'origine, contrairement à `docker import` qui crée une image à une seule couche à partir d'un filesystem exporté.

---

### Exercice 5 — Limiter le CPU d'un conteneur

**Votre réponse :**
```bash
docker run -d --cpus="0.5" nginx
```
✅ Correcte.
> **Mini explication :** `--cpus="0.5"` limite le conteneur à la moitié d'un cœur CPU, peu importe le nombre de cœurs disponibles sur la machine hôte.

---

### Exercice 6 — Conteneur en lecture seule

**Votre réponse :**
```bash
docker
```
⚠️ Incomplète.

**Version corrigée :**
```bash
docker run -d --read-only --tmpfs /tmp nginx
```
> **Mini explication :** `--read-only` rend le filesystem racine du conteneur immuable (protection contre une écriture malveillante ou accidentelle). Comme certaines applications ont besoin d'écrire des fichiers temporaires, `--tmpfs /tmp` ouvre une exception : un montage en mémoire (RAM), donc rapide et automatiquement vidé à l'arrêt du conteneur.

---

### Exercice 7 — Retirer des capabilities Linux

**Votre réponse :**
```bash
docker run -d
```
⚠️ Incomplète.

**Version corrigée :**
```bash
docker run -d --cap-drop=ALL --cap-add=NET_BIND_SERVICE nginx
```
> **Mini explication :** `--cap-drop=ALL` retire toutes les capabilities Linux du conteneur (principe du moindre privilège). Nginx a normalement besoin d'écouter sur le port 80 (port < 1024, dit "privilégié"), ce qui nécessite la capability `NET_BIND_SERVICE` — on la réajoute donc explicitement, sans redonner tout le reste.

---

### Exercice 8 — HEALTHCHECK dans le Dockerfile

**Votre réponse :**
```dockerfile
from node:18
CMD ["curl", "-f", "http://localhost"]
INTERVAL 15s
CONDITION 3s unhealthy
```
❌ Plusieurs erreurs :
- L'image de base devait être `nginx:alpine` (l'exercice précise "pour une image nginx:alpine"), pas `node:18`.
- `HEALTHCHECK` est une **instruction unique** avec des options (`--interval`, `--retries`, etc.) suivie de `CMD`, pas trois instructions séparées (`CMD`, `INTERVAL`, `CONDITION` n'existent pas en tant qu'instructions Dockerfile).

**Version corrigée :**
```dockerfile
FROM nginx:alpine
HEALTHCHECK --interval=15s --retries=3 CMD curl -f http://localhost/ || exit 1
```
> **Mini explication :** `HEALTHCHECK` définit directement dans l'image la commande que Docker exécutera périodiquement pour juger si le conteneur est en bonne santé (`healthy`/`unhealthy`), ce qui alimente ensuite les `depends_on: condition: service_healthy` vus en Compose. `--retries=3` signifie qu'il faut 3 échecs consécutifs avant de basculer en `unhealthy`.

---

### Exercice 9 — LABEL de métadonnées

**Votre réponse :** Aucune idée.

**Correction :**
```dockerfile
LABEL maintainer="votre-email@exemple.com"
LABEL version="1.0"
```
> **Mini explication :** `LABEL` ajoute des métadonnées clé-valeur à l'image, visibles via `docker inspect`. Elles ne changent rien au fonctionnement du conteneur mais servent à documenter l'image (auteur, version, description...), utile pour l'organisation et l'automatisation (scripts qui filtrent les images par label).

---

### Exercice 10 — Connecter un conteneur déjà lancé à un réseau

**Votre réponse :**
```bash
docker network connect shop_network shop_api
```
✅ Correcte.
> **Mini explication :** contrairement à `--network` au moment du `docker run`, `docker network connect` permet d'ajouter un réseau à un conteneur **déjà démarré**, sans le redémarrer — un conteneur peut être connecté à plusieurs réseaux simultanément.

---

### Exercice 11 — Inspecter les connexions réseau

**Votre réponse :**
```bash
docker network inspect shop_network
```
✅ Correcte.
> **Mini explication :** cette commande retourne un JSON listant, entre autres, tous les conteneurs actuellement connectés au réseau (`Containers`), avec leur adresse IP interne.

---

### Exercice 12 — Espace disque utilisé par Docker

**Votre réponse :** Pas de réponse fournie.

**Correction :**
```bash
docker system df
```
> **Mini explication :** cette commande résume l'espace disque consommé par les images, conteneurs, volumes et le cache de build, avec la part "récupérable" (ex : images non utilisées par un conteneur actif) — un bon réflexe avant de faire un `docker system prune`.

---

### Exercice 13 — Historique des couches d'une image

**Votre réponse :** Pas de réponse fournie.

**Correction :**
```bash
docker history ubuntu-curl:1.0
```
> **Mini explication :** affiche chaque couche (layer) de l'image, l'instruction Dockerfile qui l'a créée, et sa taille — pratique pour repérer quelle instruction alourdit le plus l'image (par exemple un `RUN apt-get install` sans nettoyage du cache).

---

### Exercice 14 — Logs avec filtres

**Votre réponse :**
```bash
docker logs shop_api --last-history 50
```
❌ `--last-history` n'est pas un flag valide de `docker logs`.

**Version corrigée :**
```bash
docker logs --tail 50 --timestamps shop_api
```
> **Mini explication :** `--tail 50` limite l'affichage aux 50 dernières lignes (au lieu de tout l'historique des logs), et `--timestamps` (ou `-t`) préfixe chaque ligne par sa date/heure exacte — utile pour situer un incident dans le temps.

---

### Exercice 15 — Limiter la taille des logs

**Votre réponse :** Pas de réponse fournie.

**Correction :**
```bash
docker run -d --name shop_api \
  --log-driver json-file \
  --log-opt max-size=10m \
  --log-opt max-file=3 \
  shop-api:1.0
```
> **Mini explication :** par défaut, le driver `json-file` de Docker peut laisser grossir les logs indéfiniment et saturer le disque. `max-size=10m` limite chaque fichier de log à 10 Mo, et `max-file=3` ne conserve que les 3 derniers fichiers (rotation automatique, les plus anciens sont supprimés).

---

## Bilan global

| Compétence | Statut |
|---|---|
| `docker commit` (image depuis un conteneur) | ❌ À revoir — confusion avec une commande inexistante |
| `docker diff` | ❌ Ciblait l'image au lieu du conteneur |
| `docker save`/`load` vs `export`/`import` | ❌ Confusion entre les deux paires de commandes |
| Limitation CPU (`--cpus`) | ✅ Maîtrisé |
| `--read-only` + `tmpfs` | ❌ Non traité |
| `--cap-drop`/`--cap-add` | ❌ Non traité |
| `HEALTHCHECK` dans le Dockerfile | ❌ Syntaxe et image de base incorrectes |
| `LABEL` | ❌ Non traité |
| `docker network connect`/`inspect` | ✅ Maîtrisé |
| `docker system df` | Non traité |
| `docker history` | Non traité |
| Filtres de `docker logs` (`--tail`, `--timestamps`) | ❌ Flag inventé |
| Rotation des logs (`--log-opt`) | Non traité |

**Points à retravailler en priorité :** la distinction `export/import` (conteneur) vs `save/load` (image), la syntaxe de l'instruction `HEALTHCHECK`, et les options de sécurité/ressources (`--read-only`, `--cap-drop`) qui n'ont pas encore été pratiquées.