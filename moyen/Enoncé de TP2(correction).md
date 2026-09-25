# Correction — TP 12 (niveau moyen) : Réseaux Docker en profondeur

Ce document reprend chaque exercice, votre réponse, la correction et une mini explication.

---

### Exercice 1 — Modes réseau : bridge par défaut vs host

**Votre réponse :**
```bash
docker run -d \
--name shop_api shop-api:1.0\
--port 4000 \
--drive-opt bridge

docker run -d\
--rename shop_api_host shop_api\
--port 4000\
--drive-opt bridge
```
❌ Plusieurs erreurs :
- Le nom de l'image (`shop-api:1.0`) doit être placé **en dernier**, après toutes les options — sinon Docker interprète ce qui suit comme la commande à exécuter dans le conteneur.
- `--port` n'existe pas comme flag ; c'est `-p <hôte>:<conteneur>`.
- `--drive-opt bridge` n'est pas une option valide. `bridge` est déjà le driver par défaut, pas besoin de le préciser sauf via `--network bridge`.
- `--rename` n'est pas un flag de `docker run` (il n'existe même pas en tant que sous-commande standalone — pour renommer un conteneur *existant* on utilise `docker rename`, mais ici il fallait un tout **nouveau** conteneur en mode `host`).

**Version corrigée :**
```bash
docker run -d --name shop_api -p 4000:4000 shop-api:1.0

docker run -d --name shop_api_host --network host shop-api:1.0
```
> **Mini explication :** en mode `bridge` (par défaut), le conteneur vit dans son propre namespace réseau isolé — le port 4000 n'est accessible depuis l'hôte que si on le publie explicitement avec `-p`. En mode `host`, le conteneur partage directement la pile réseau de la machine hôte : l'application écoute alors immédiatement sur le port 4000 de l'hôte, sans aucun mapping, mais on perd l'isolation réseau et deux conteneurs ne peuvent plus utiliser le même port simultanément.

---

### Exercice 2 — Mode réseau none

**Votre réponse :**
```bash
docker run -d\
--name shop_worker alpine\
--drive-opt none
```
❌ `--drive-opt none` n'existe pas. Le mode réseau se précise avec `--network none`, et il doit être placé **avant** le nom de l'image. De plus, `alpine` seul s'arrête immédiatement sans commande à exécuter en continu.

**Version corrigée :**
```bash
docker run -d --name shop_worker --network none alpine sleep infinity
```
> **Mini explication :** `--network none` retire complètement le conteneur de toute interface réseau (à part `loopback`). C'est pertinent pour un worker qui ne fait que lire/écrire des fichiers locaux ou une file de tâches partagée par volume : aucune surface d'attaque réseau, et aucune fuite de données possible via le réseau.

---

### Exercice 3 — Créer un réseau avec un sous-réseau personnalisé

**Votre réponse :**
```bash
docker create network shop_backend_net \
--ip4 172.28.0.0/16 \
--drive-opt bridge
```
❌ Plusieurs erreurs :
- La commande est `docker network create`, pas `docker create network`.
- `--ip4` n'existe pas ; l'option pour définir un sous-réseau est `--subnet`.
- `--drive-opt` n'existe pas ; le driver se précise avec `--driver`.

**Version corrigée :**
```bash
docker network create --driver bridge --subnet 172.28.0.0/16 shop_backend_net
```
> **Mini explication :** `--subnet` impose la plage d'adresses IP que Docker utilisera pour attribuer des IP aux conteneurs connectés à ce réseau, au lieu de laisser Docker choisir une plage automatiquement.

---

### Exercice 4 — Assigner une IP fixe à un conteneur

**Votre réponse :**
```bash
docker run -d \
--name shop_db \
--network shop_backend_net\
--ip4 172.28.0.10
```
❌ Il manque le nom de l'image (`mysql`) et la variable `MYSQL_ROOT_PASSWORD` obligatoire pour que MySQL démarre. `--ip4` n'existe pas, le flag correct est `--ip`.

**Version corrigée :**
```bash
docker run -d --name shop_db \
--network shop_backend_net \
--ip 172.28.0.10 \
-e MYSQL_ROOT_PASSWORD=secret123 \
mysql:8
```
> **Mini explication :** `--ip` ne fonctionne que sur un réseau **personnalisé** (pas sur le bridge par défaut), et seulement si ce réseau a un sous-réseau explicite déclaré — exactement ce que vous avez créé à l'exercice 3.

---

### Exercice 5 — Alias réseau

**Votre réponse :**
```bash
docker network connect -d \
--name shop_db \
--network shop_backend_net\
--alias database
```
❌ `docker network connect` a une syntaxe fixe : `docker network connect [OPTIONS] RESEAU CONTENEUR`. Les flags `-d`, `--name` et `--network` n'existent pas pour cette sous-commande — le réseau et le conteneur sont des arguments positionnels, pas des options.

**Version corrigée :**
```bash
docker network connect --alias database shop_backend_net shop_db
```
> **Mini explication :** `--alias` ajoute un nom DNS supplémentaire résolvable uniquement sur ce réseau, en plus du nom du conteneur — les deux (`shop_db` et `database`) pointeront vers la même IP depuis n'importe quel conteneur du réseau `shop_backend_net`.

---

### Exercice 6 — Isoler le frontend de la base de données

**Votre réponse :**
```bash
docker network create shop_frontend_net

docker network connect shop_frontend_net shop_proxy
docker network connect shop_frontend_net shop_frontend

docker network connect shop_frontend_net shop_api
docker network connect shop_backend_net shop_api

docker network connect shop_backend_net shop_db
```
✅ La logique est correcte : `shop_db` reste uniquement sur `shop_backend_net`, `shop_api` fait le pont entre les deux réseaux, et `shop_proxy`/`shop_frontend` restent cantonnés à `shop_frontend_net`. C'est exactement l'architecture demandée.

> **Mini point d'attention :** si ces conteneurs avaient été lancés initialement sans `--network`, ils sont aussi connectés au réseau `bridge` par défaut de Docker. Pour une isolation totale, il faudrait aussi les en déconnecter avec `docker network disconnect bridge <conteneur>`, sinon `shop_frontend` pourrait potentiellement encore joindre `shop_db` via ce réseau par défaut si celui-ci y est aussi connecté.

---

### Exercice 7 — Vérifier l'isolation réseau

**Votre réponse :**
```bash
docker compose exec -it --name shop_db --port 3306
```
❌ Cette commande est invalide : `docker compose exec` prend un nom de service puis une commande à exécuter, pas des flags `--name`/`--port`. Il fallait exécuter un outil de test de connexion (`nc`) **depuis** `shop_proxy` **vers** `shop_db:3306`.

**Version corrigée :**
```bash
docker exec -it shop_proxy sh -c "nc -zv shop_db 3306"
```
> **Mini explication :** `nc -zv` (netcat en mode "zero-I/O", verbeux) tente une simple connexion TCP sans envoyer de données, et affiche si la connexion réussit ou échoue — parfait pour vérifier l'isolation réseau sans avoir besoin du client MySQL.

---

### Exercice 8 — Résolution DNS interne

**Votre réponse :**
```bash
docker exec -it bash shop_api
```
❌ L'ordre des arguments est inversé : `docker exec` attend `docker exec [OPTIONS] CONTENEUR COMMANDE`, donc `bash` doit venir **après** le nom du conteneur. De plus, cette commande ouvre juste un terminal, elle ne résout aucun DNS.

**Version corrigée :**
```bash
docker exec -it shop_api nslookup shop_db
# ou
docker exec -it shop_api getent hosts shop_db
```
> **Mini explication :** `nslookup` (ou `getent hosts`, souvent plus fiable sur les images Alpine minimalistes) interroge le serveur DNS embarqué dans le moteur Docker (`127.0.0.11`), qui résout automatiquement les noms de conteneurs et alias vers leurs IP internes.

---

### Exercice 9 — Inspecter le trafic entre deux conteneurs

**Votre réponse :**
```bash
docker network inspect shop_backend_net
```
✅ Correcte.
> **Mini explication :** le champ `Containers` du JSON retourné liste tous les conteneurs connectés à ce réseau, avec leur IP et leur adresse MAC.

---

### Exercice 10 — Débrancher un conteneur d'un réseau à chaud

**Votre réponse :**
```bash
docker network disconnect shop_frontend_net shop_api
```
✅ Correcte.
> **Mini explication :** cette commande retire `shop_api` de `shop_frontend_net` sans le redémarrer ; il reste connecté à `shop_backend_net` si c'était déjà le cas (comme fait à l'exercice 6).

---

### Exercice 11 — Réseau dédié pour un outil de debug

**Votre réponse :**
```bash
docker run -d\
--name shop_debug\
nicolaka/netshoot
--network shop_backend_net
```
❌ Deux erreurs :
- `--network shop_backend_net` est placé **après** le nom de l'image : Docker l'interprète comme un argument passé à la commande du conteneur, pas comme une option `docker run`.
- L'exercice demandait un mode **interactif** (`-it`) pour pouvoir taper des commandes de diagnostic, pas `-d` (détaché).

**Version corrigée :**
```bash
docker run -it --name shop_debug --network shop_backend_net nicolaka/netshoot
```
> **Mini explication :** en Docker, tout ce qui suit le nom de l'image est traité comme la commande à exécuter dans le conteneur (équivalent du `CMD`), pas comme des options `docker run` — l'ordre des arguments est donc strict : `docker run [OPTIONS] IMAGE [COMMANDE]`.

---

### Exercice 12 — Capturer le trafic réseau

**Votre réponse :**
```bash
docker
```
⚠️ Incomplète (déjà validée dans un échange précédent, mais absente de votre copie finale).

**Correction :**
```bash
tcpdump -nn -i any dst port 3306
```
> **Mini explication :** `-nn` désactive la résolution DNS/ports pour un affichage plus rapide et lisible, `-i any` capture sur toutes les interfaces du conteneur, et `dst port 3306` filtre uniquement les paquets à destination de MySQL. Attention : `tcpdump` nécessite les capabilities `NET_ADMIN` et `NET_RAW`, à ajouter au `docker run` du conteneur `shop_debug` (`--cap-add=NET_ADMIN --cap-add=NET_RAW`).

---

### Exercice 13 — Lister tous les réseaux liés au projet

**Votre réponse :**
```bash
docker network ls
```
⚠️ Fonctionne mais liste **tous** les réseaux, pas seulement ceux contenant `shop` comme demandé.

**Version corrigée :**
```bash
docker network ls --filter name=shop
```
> **Mini explication :** `--filter name=<motif>` applique un filtre côté serveur Docker directement sur le nom, plutôt que de tout lister puis chercher visuellement (ou via `grep`, moins fiable en script).

---

### Exercice 14 — Réseau en lecture d'un docker-compose.yml existant

**Votre réponse :**
```yaml
networks :
    - shop_backend_net
    - shop_frontend_net
```
❌ Ce n'est pas une commande, c'est un extrait de YAML — l'exercice demandait une commande shell pour **découvrir** le nom réel généré par Compose, pas de réécrire la déclaration des réseaux dans le fichier.

**Version corrigée :**
```bash
docker network ls --filter name=shop_backend_net
```
> **Mini explication :** Compose préfixe généralement chaque ressource par le nom du projet (dossier contenant le `docker-compose.yml`), par exemple `notesapi_shop_backend_net`. Ce filtre permet de retrouver rapidement le nom réel sans avoir à mémoriser la convention exacte de préfixage (qui peut varier légèrement selon la version de Compose).

---

### Exercice 15 — Nettoyer les réseaux orphelins

**Votre réponse :**
```bash
docker
```
⚠️ Incomplète.

**Correction :**
```bash
docker network prune
```
> **Mini explication :** cette commande supprime tous les réseaux personnalisés qui ne sont connectés à **aucun** conteneur (les réseaux par défaut `bridge`, `host` et `none` ne sont jamais supprimés). Docker demande une confirmation avant de procéder, sauf si on ajoute `-f`.

---

## Bilan global

| Compétence | Statut |
|---|---|
| Syntaxe de base `docker run` (ordre options/image/commande) | ❌ Erreur récurrente : options placées après le nom de l'image |
| Modes réseau bridge/host/none | ❌ Flags inventés (`--drive-opt`), logique conceptuelle correcte |
| Création de réseau avec sous-réseau (`docker network create`) | ❌ Commande et flags incorrects |
| IP fixe et alias (`--ip`, `--alias`) | ❌ Flags inventés (`--ip4`), syntaxe `network connect` mal comprise |
| Architecture multi-réseaux (isolation frontend/backend) | ✅ Très bien maîtrisé — la logique métier est juste |
| Test de connectivité (`nc`, `nslookup`) | ❌ Confusion entre `docker exec` et `docker compose exec`, ordre des arguments |
| Inspection réseau (`docker network inspect`) | ✅ Maîtrisé |
| Déconnexion à chaud (`docker network disconnect`) | ✅ Maîtrisé |
| Filtrage (`--filter name=`) | ⚠️ Non utilisé, réponses trop génériques |
| Nettoyage (`docker network prune`) | Non traité |

**Point à retravailler en priorité :** l'ordre strict des arguments dans `docker run [OPTIONS] IMAGE [COMMANDE]` — plusieurs réponses placent des options après le nom de l'image, ce qui les rend silencieusement invalides (elles sont interprétées comme la commande du conteneur). En revanche, la compréhension **conceptuelle** de la segmentation réseau (exercice 6) est un vrai point fort.