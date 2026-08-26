# Correction TP6 — Docker Compose (bases)

## Exercices 1 à 6 — Le fichier `docker-compose.yml`

### Erreurs repérées dans la version soumise

| Élément | Version soumise | Problème | Version corrigée |
|---|---|---|---|
| Version | `version : 3.8` | espace avant `:` (interdit en YAML) | `version: "3.8"` |
| Image | `- image : nginx` | tiret en trop, `image:` n'est pas une liste | `image: nginx` |
| Port | `port : 8080:80` | mauvaise clé (doit être au pluriel) + pas une liste | `ports:` puis `- "8080:80"` |
| Dépendance | `depends_on:` suivi de `db` sans tiret | `depends_on:` est une liste | `depends_on:` puis `- db` |
| Variable d'env | `MYSQL_ROOT_PASSWORD = secret123` | espaces autour du `=` | `MYSQL_ROOT_PASSWORD=secret123` |
| Volume (service) | `volume :` | mauvaise clé (doit être au pluriel) | `volumes:` |
| Montage volume | `- "/var/lib/mysql "` | nom du volume manquant + espace final | `- db_data:/var/lib/mysql` |
| Déclaration volume | `db_data` seul | doit être une clé avec `:` | `db_data:` |

**Règle générale à retenir** : en YAML, l'indentation et la syntaxe sont très strictes. Un espace mal placé, un `:` manquant, ou une clé au singulier au lieu du pluriel (`port` vs `ports`) suffit à faire planter ou mal interpréter le fichier.

### ✅ Fichier `docker-compose.yml` corrigé

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

---

## Exercice 7 — Lancer la stack

**Correction :**
```bash
docker compose up -d
```
**Explication** : `docker run` sert à lancer un seul conteneur à partir d'une image, il ne sait pas lire un fichier `docker-compose.yml`. Pour démarrer une stack complète, on utilise `docker compose up`, qui lit automatiquement le fichier du dossier courant (`-d` pour le mode détaché).

---

## Exercice 8 — Voir les logs de la stack

**Correction :**
```bash
docker compose logs
```
**Explication** : `docker logs` ne fonctionne que pour un seul conteneur nommé explicitement. Pour voir les logs de **tous les services** d'une stack en une seule commande, il faut `docker compose logs` (avec `-f` en plus pour les suivre en direct).

---

## Exercice 9 — Lister les services actifs

**Correction :**
```bash
docker compose ps
```
**Explication** : contrairement à `docker ps` qui montre tous les conteneurs de la machine, `docker compose ps` n'affiche que ceux liés au `docker-compose.yml` du dossier courant, avec leur statut.

---

## Exercice 10 — Arrêter et tout supprimer

**Correction :**
```bash
docker compose down
```
**Explication** : arrête et supprime tous les conteneurs et le réseau créés par le `docker-compose.yml`, mais **conserve les volumes nommés** par défaut (les données de `db_data` restent intactes).

👉 Pour supprimer aussi les volumes (et donc perdre définitivement les données) :
```bash
docker compose down -v
```

---

## 📌 Résumé des commandes `docker compose` vues dans ce TP

| Action | Commande |
|---|---|
| Lancer la stack en arrière-plan | `docker compose up -d` |
| Voir tous les logs | `docker compose logs` |
| Voir l'état des services | `docker compose ps` |
| Arrêter et nettoyer (garde les volumes) | `docker compose down` |
| Arrêter et tout supprimer (avec volumes) | `docker compose down -v` |