# 📋 Correction annotée — Stack API + Cache Redis

Pour chaque question : **ta réponse**, **la correction**, et une **mini-explication** de ce qui change et pourquoi.

---

## Partie 1 — Préparation

### Question 1

**Ta réponse :**
```bash
docker network create api_network
```

**Correction :** ✅ Correcte, rien à changer.

**Explication :** C'est la syntaxe exacte pour créer un réseau bridge personnalisé.

---

### Question 2

**Ta réponse :**
```bash
docker volume create redis_data
```

**Correction :** ✅ Correcte, rien à changer.

**Explication :** Crée un volume nommé, géré par Docker, indépendant du cycle de vie des conteneurs.

---

## Partie 2 — Cache Redis

### Question 3

**Ta réponse :**
```bash
docker run -d api_redis --network api_network --volume redis_data:/data
```

**Correction :**
```bash
docker run -d \
  --name api_redis \
  --network api_network \
  --volume redis_data:/data \
  redis
```

**Explication :** Deux erreurs importantes :
1. Il manque le flag `--name` : tel que tu l'as écrit, `api_redis` est interprété comme le **nom de l'image** à lancer (pas comme le nom du conteneur), ce qui provoquerait une erreur *"Unable to find image 'api_redis'"*.
2. Il manque carrément le **nom de l'image à la fin** (`redis`) : sans ça, Docker ne sait pas quelle image démarrer.

---

### Question 4

**Ta réponse :**
```bash
docker ps
```

**Correction :** ✅ Correcte. Pour être plus précis on peut filtrer :
```bash
docker ps --filter "name=api_redis"
```

**Explication :** `docker ps` liste les conteneurs en cours d'exécution ; il faut regarder que `api_redis` apparaît avec le statut `Up`.

---

### Question 5

**Ta réponse :**
```bash
docker logs api_redis
```

**Correction :** ✅ Correcte dans l'idée, mais la question demandait explicitement de **repérer une ligne précise**. On peut filtrer :
```bash
docker logs api_redis | grep "Ready to accept connections"
```

**Explication :** Ça évite de parcourir tous les logs à l'œil et prouve que tu as bien vérifié la ligne demandée.

---

## Partie 3 — API Node.js (Dockerfile)

### Question 6

**Ta réponse — `package.json` :**
```json
{
    "name" : "api", 
    "version" : "1.0.0"
    "main" : "server.js", 
    "dependancies" : {
        "name" : "redis", 
        "version" : "3.12"
    }
}
```

**Correction :**
```json
{
  "name": "api",
  "version": "1.0.0",
  "main": "server.js",
  "dependencies": {
    "redis": "^4.6.13"
  }
}
```

**Explication :**
- Il manque une **virgule** après `"1.0.0"` → ton JSON est invalide tel quel (erreur de syntaxe, `npm install` échouerait).
- `"dependancies"` est mal orthographié : le bon mot-clé npm est `"dependencies"`.
- La structure `dependencies` est fausse : ce n'est pas un objet `{name, version}`, mais une liste de paires **`"nom_du_package": "version"`**. Ici : `"redis": "^4.6.13"`.

**⚠️ Il manque aussi `server.js` dans ta réponse.** C'était une partie obligatoire de la question 6 (le fichier qui incrémente le compteur dans Redis). Voici ce qu'il fallait fournir :
```javascript
const http = require('http');
const redis = require('redis');

const client = redis.createClient({
  socket: {
    host: process.env.REDIS_HOST || 'localhost',
    port: 6379
  }
});

client.on('error', (err) => console.error('Erreur Redis :', err));

async function start() {
  await client.connect();

  const server = http.createServer(async (req, res) => {
    if (req.url === '/') {
      const visites = await client.incr('visites');
      res.writeHead(200, { 'Content-Type': 'text/plain; charset=utf-8' });
      res.end(`Nombre de visites : ${visites}`);
    } else {
      res.writeHead(404);
      res.end('Not found');
    }
  });

  server.listen(4000, () => console.log('Serveur démarré sur le port 4000'));
}

start();
```

**Ta réponse — `Dockerfile` :**
```dockerfile
FROM node:18
WORKDIR /app
COPY package.json package-lock.json ./
RUN "npm install"
COPY . .
EXPOSE 4000
CMD ["node", "server.js"]
```

**Correction :**
```dockerfile
FROM node:18
WORKDIR /app
COPY package.json ./
RUN npm install
COPY . .
EXPOSE 4000
CMD ["node", "server.js"]
```

**Explication :**
- `COPY package.json package-lock.json ./` : tu copies un fichier `package-lock.json` qui **n'existe pas** dans ce projet minimal (il n'a jamais été créé). `COPY` échoue si le fichier source n'existe pas → le build plante. Il fallait ne copier que `package.json`.
- `RUN "npm install"` : syntaxe invalide. Avec des guillemets autour de toute la commande, Docker/le shell essaie d'exécuter un unique programme littéralement nommé `npm install` (avec l'espace dedans), ce qui échoue (*"npm install: command not found"*). Il faut écrire simplement `RUN npm install` (sans guillemets), ou en forme exec : `RUN ["npm", "install"]`.
- Le reste de la structure (WORKDIR, EXPOSE, CMD) est bon, bravo 👍.

**❌ Il manque la Question 7 : la construction de l'image !**
```bash
cd api
docker build -t api-app:1.0 .
```
Sans ça, l'image `api-app:1.0` utilisée en question 8 n'existe pas.

---

## Partie 4 — Lancement de l'API

### Question 8

**Ta réponse :**
```bash
docker run --name api-app -n api_network -p 4000 -e REDIS_HOST=${REDIS_HOST}
```

**Correction :**
```bash
docker run -d \
  --name api_app \
  --network api_network \
  -p 4000:4000 \
  -e REDIS_HOST=api_redis \
  api-app:1.0
```

**Explication :** plusieurs erreurs à corriger :
- `-n` n'est **pas** un alias valide pour `--network` en Docker (ça n'existe pas comme flag de `docker run`) → utiliser `--network` (ou `--net`).
- `-p 4000` sans le format `hôte:conteneur` publie le port du conteneur vers un **port aléatoire** de l'hôte, pas forcément le 4000. Il faut écrire `-p 4000:4000`.
- `-e REDIS_HOST=${REDIS_HOST}` : ça utilise une variable d'environnement de **ton shell**, qui n'est probablement pas définie (elle serait vide). La consigne demande de fixer la valeur en dur : `-e REDIS_HOST=api_redis`.
- Il manque le **nom de l'image à lancer** (`api-app:1.0`) tout à la fin de la commande.
- Il manque `-d` pour lancer en tâche de fond (sinon la commande "bloque" ton terminal, il tourne en avant-plan).
- Nom du conteneur : la consigne demande `api_app` (underscore), tu as écrit `api-app` (tiret) — ce n'est pas bloquant techniquement, mais toutes les commandes suivantes (`docker exec api_app...`, `docker cp api_app:...`) supposeront ce nom exact, donc autant rester cohérent.

---

### Question 9

**Ta réponse :**
```bash
curl http://localhost:4000
```

**Correction :** ✅ Correcte, mais pour valider complètement la question il faut la relancer plusieurs fois :
```bash
curl http://localhost:4000
curl http://localhost:4000
curl http://localhost:4000
```

**Explication :** La consigne demande aussi de vérifier que **le compteur s'incrémente** à chaque rechargement — un seul appel ne suffit pas à le prouver.

---

## Partie 5 — Vérifications & manipulations

### Question 10

**Ta réponse :**
```bash
docker ps -a
```

**Correction :**
```bash
docker ps
```

**Explication :** `docker ps -a` liste **tous** les conteneurs, y compris ceux qui sont arrêtés. La consigne demande précisément les conteneurs **en cours d'exécution**, donc `docker ps` (sans `-a`) est la bonne commande ici.

---

### Question 11

**Ta réponse :**
```bash
docker ping --name api_redis
```

**Correction :**
```bash
docker exec -it api_app getent hosts api_redis
```
ou, si tu veux un vrai ping (à installer, car absent de l'image `node:18`) :
```bash
docker exec -it api_app sh -c "apt-get update && apt-get install -y iputils-ping && ping -c 3 api_redis"
```

**Explication :** `docker ping` **n'existe pas** en tant que commande Docker. Pour exécuter une commande à l'intérieur d'un conteneur déjà lancé, il faut utiliser `docker exec`. Et attention : l'image `node:18` ne fournit pas l'utilitaire `ping` par défaut, donc `getent hosts` (qui vérifie juste la résolution DNS) est souvent plus simple et suffisant pour prouver que les deux conteneurs communiquent.

---

### Question 12

**Ta réponse :**
```bash
docker copy server.js api_app /sauvegarde_api
```

**Correction :**
```bash
mkdir -p sauvegarde_api
docker cp api_app:/app/server.js ./sauvegarde_api/
```

**Explication :**
- La commande s'appelle `docker cp`, pas `docker copy`.
- L'ordre des arguments est `docker cp <source> <destination>`. La source ici est un fichier **dans** le conteneur, donc il faut préfixer par le nom du conteneur et le **chemin complet dans le conteneur** : `api_app:/app/server.js`.
- La destination est un dossier **local** (relatif ou absolu), pas `/sauvegarde_api` à la racine du système — sauf si c'est vraiment voulu. En général on utilise un chemin relatif comme `./sauvegarde_api/`.

---

### Question 13

**Ta réponse :**
```bash
docker inspect api_redis
```

**Correction :** Fonctionnellement correcte pour explorer le conteneur, mais pour répondre précisément à "quelle est son IP sur `api_network`", il vaut mieux filtrer :
```bash
docker inspect -f '{{.NetworkSettings.Networks.api_network.IPAddress}}' api_redis
```

**Explication :** `docker inspect` seul renvoie un très long JSON. La question demande une info précise (l'adresse IP interne sur le réseau `api_network`) : utiliser `-f` (format Go template) permet d'extraire directement la valeur demandée plutôt que de la chercher à l'œil dans tout le JSON.

---

### Question 14

**Ta réponse :** *(vide)*

**Correction :**
```bash
docker update --memory="200m" --memory-swap="200m" api_app
```
Si cela échoue (selon le moteur/l'OS), il faut recréer le conteneur :
```bash
docker stop api_app
docker rm api_app
docker run -d --name api_app --network api_network -p 4000:4000 \
  -e REDIS_HOST=api_redis --memory="200m" api-app:1.0
```

**Explication :** Oui, il est possible de limiter la mémoire **a posteriori**, sans recréer le conteneur, grâce à `docker update` (fonctionnalité disponible sur les hôtes Linux avec cgroups, ce qui est le cas de Docker Desktop). Si le moteur ne supporte pas ce changement à chaud, la seule solution est de supprimer le conteneur et de le relancer avec le flag `--memory` dès la création — la limite mémoire ne peut alors être fixée qu'à la création si `docker update` n'est pas disponible.

---

## Partie 6 — Nettoyage

### Question 15

**Ta réponse :**
```bash
docker rm -f api_redis 
docker rm -f api_app
```

**Correction :** ✅ Correcte, rien à changer.

**Explication :** `docker rm -f` arrête (force) et supprime le conteneur en une seule commande, exactement ce qui était demandé.

---

### Question 16

**Ta réponse :**
```bash
docker network rm api_network
```

**Correction :** ✅ Correcte, rien à changer.

---

### Question 17

**Ta réponse :** *(vide)*

**Correction :**
> Ça dépend de l'usage voulu :
> - **Non**, si tu comptes relancer la stack plus tard et garder l'historique du compteur de visites : les données Redis (dont la clé `visites`) sont persistées dans `redis_data`. Le supprimer ferait perdre ces données définitivement.
> - **Oui**, si le projet est terminé et que ces données ne servent plus à rien, on peut nettoyer complètement avec :
> ```bash
> docker volume rm redis_data
> ```

**Explication :** Un volume nommé existe **indépendamment** du conteneur qui l'utilise — le supprimer est une décision à part, à prendre selon si on veut garder la donnée ou pas.

---

### Question 18

**Ta réponse :** *(vide)*

**Correction :**
```bash
docker rmi api-app:1.0
docker rmi redis
```

**Explication :** Ces commandes ne fonctionnent que si **plus aucun conteneur** (même arrêté) ne référence ces images — c'est pour ça que l'ordre des opérations compte : il faut avoir fait la question 15 (suppression des conteneurs) avant. Si un conteneur arrêté existe encore et référence l'image, Docker refusera avec une erreur du type *"image is being used by stopped container"*.

---

## 🎯 Bilan

| # | Statut |
|---|---|
| Q1, Q2, Q4, Q5, Q9, Q15, Q16 | ✅ Bonnes (parfois à préciser) |
| Q3, Q8, Q10, Q11, Q12, Q13, Q6 (Dockerfile) | ⚠️ Erreurs de syntaxe ou d'arguments |
| Q6 (package.json + server.js manquant), Q7 (absente) | ❌ Incomplet |
| Q14, Q17, Q18 | ❌ Vides |

Points à retravailler en priorité : la syntaxe de `docker run` (flags `--name`, `--network`, `-p host:container`), la distinction `docker cp` vs `docker copy` (qui n'existe pas), et bien lire le JSON de `package.json` avant de le valider.