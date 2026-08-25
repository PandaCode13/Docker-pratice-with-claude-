question 1 :

docker network create app_network

correction : ✅ Correct, rien à changer.
Mini explication : crée un réseau Docker nommé `app_network`, qui permettra à plusieurs conteneurs de communiquer entre eux par leur nom.

question 2 :

docker volume create db_data

correction : ✅ Correct, rien à changer.
Mini explication : crée un volume nommé `db_data`, un espace de stockage géré par Docker et indépendant du cycle de vie des conteneurs.

question 3 :

docker run -d --name db --network app_network --volume db_data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=secret123 mysql

correction : ✅ Correct, rien à changer.
Mini explication : `--volume` est juste la forme longue de `-v`, donc c'est équivalent et parfaitement valide.

question 4 :

docker ps

correction : ✅ Correct, rien à changer.
Mini explication : affiche uniquement les conteneurs en cours d'exécution.

question 5 :

docker logs db

correction : ✅ Correct, rien à changer.
Mini explication : affiche les logs du conteneur `db` pour vérifier que MySQL a bien démarré.

question 6 :

**app.js** — ❌ petite faute de frappe :
```javascript
res;end("Hello Docker");
```
✅ Correction :
```javascript
const http = require("http");

const server = http.createServer((req, res) => {
    res.writeHead(200, {'Content-Type': 'text/plain'});
    res.end("Hello Docker");
});

server.listen(3000, () => {
    console.log("Serveur démarré sur le port 3000");
});
```
Mini explication : tu as écrit `res;end(...)` (point-virgule) au lieu de `res.end(...)` (point). En JavaScript, le point `.` sert à accéder à une méthode d'un objet — ici `end()` est une méthode de l'objet `res`. Avec un `;`, JavaScript interprète ça comme deux instructions séparées, et `end("Hello Docker")` n'existe pas tout seul → erreur.

**Dockerfile** — ❌ deux erreurs :
```dockerfile
WORKDIR app
RUN "node app.js"
```
✅ Correction :
```dockerfile
FROM node:18
WORKDIR /app
COPY . .
EXPOSE 3000
CMD ["node", "app.js"]
```
Mini explication :
- `WORKDIR app` fonctionne techniquement (chemin relatif), mais la bonne pratique est un chemin **absolu** : `/app`.
- `RUN "node app.js"` est une erreur importante : `RUN` exécute une commande **pendant la construction** de l'image, donc ton serveur se lancerait puis s'arrêterait immédiatement à la fin du build (et resterait bloqué si jamais). Ce qu'il faut ici, c'est `CMD`, qui définit la commande à exécuter **au démarrage du conteneur**. De plus, `RUN "node app.js"` en une seule chaîne n'est pas la bonne syntaxe même pour `RUN` — il aurait fallu `RUN node app.js` (sans guillemets autour de toute la commande) ou la forme tableau `CMD ["node", "app.js"]`.

question 7 :

docker build -t exec mon-app:1.0

correction : 
```bash
docker build -t mon-app:1.0 .
```
Mini explication : le mot `exec` n'a rien à faire ici — il est interprété par erreur comme le nom de l'image (`-t exec`), et `mon-app:1.0` devient alors un argument en trop. Il manque aussi le `.` final, qui indique le **contexte de build** (le dossier où se trouve le Dockerfile). Sans lui, la commande échoue.

question 8 :

docker run --name web --network app_network --port 3000

correction : 
```bash
docker run -d --name web --network app_network -p 3000:3000 mon-app:1.0
```
Mini explication : trois erreurs ici — il manque `-d` (sinon le conteneur reste au premier plan et bloque le terminal), `--port` n'existe pas en tant qu'option Docker (c'est `-p` ou `--publish`, avec le format `hôte:conteneur`), et il manque le nom de l'**image** à lancer (`mon-app:1.0`) tout à la fin.

question 9 :

curl http://localhost:3000

correction : ✅ Correct, rien à changer.
Mini explication : envoie une requête HTTP vers le conteneur, qui doit répondre `Hello Docker`.

question 10 :

docker exec -t it

correction : 
```bash
docker exec -it web ping db
```
Mini explication : l'énoncé demandait de pinguer `db` depuis `web`. Ta commande est incomplète : `-t it` n'a pas de sens (ce sont deux options mal écrites, il fallait `-it` collé), et il manque le nom du conteneur cible (`web`) ainsi que la commande à exécuter (`ping db`).

question 11 :

mkdir -p backup
docker cp --name web backup

correction : 
```bash
mkdir -p backup
docker cp web:/app/app.js backup/
```
Mini explication : `docker cp` n'a pas d'option `--name`. Sa syntaxe est `docker cp source destination`, où la source ici est `nom_du_conteneur:chemin_dans_le_conteneur` (`web:/app/app.js`), et la destination est le dossier local `backup/`.

question 12 :

docker inspect --name web

correction : 
```bash
docker inspect web
```
Mini explication : `docker inspect` n'a pas d'option `--name` — on donne directement le nom (ou l'ID) du conteneur en argument. 
👉 Pour l'IP précisément : `docker inspect -f '{{.NetworkSettings.Networks.app_network.IPAddress}}' web`

question 13 :

docker rm -f web (equivalent : docker stop web && docker rm web)
docker rm -f db (equivalent : docker stop db && docker rm db)

correction : ✅ Correct, rien à changer.
Mini explication : `-f` (`--force`) arrête le conteneur s'il tourne encore, puis le supprime — exactement équivalent à faire les deux commandes séparément avec `&&`.

question 14 :

docker network rm app_network

correction : ✅ Correct, rien à changer.
Mini explication : supprime le réseau, à condition qu'aucun conteneur n'y soit encore connecté.