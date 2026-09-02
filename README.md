# 📦 Docker Practice with Claude

Ce repository sert d'**espace d'apprentissage et d'entraînement à Docker**. Il regroupe des cours, des travaux pratiques (TP) et des mini-projets, tous couverts en Markdown, afin de monter en compétence pas à pas — du lancement d'un premier conteneur jusqu'à la gestion de stacks multi-conteneurs avec Docker Compose.

---

## 🗂️ Structure du projet

```
Docker-pratice-with-claude-/
├── README.md                        ← ce fichier (vue d'ensemble)
├── Résumé du dossier facile.md      ← récapitulatif condensé des TP
│
├── cours/
│   └── facile.md                    ← cours Docker complet « De A à Z » (TP1→TP9 + TP Final)
│
├── facile/                          ← les travaux pratiques (énoncés + solutions)
│   ├── Enoncé de TP1.md … Enoncé de TP9.md, Enoncé de TP Final.md
│   ├── TP1 (me).md … TP9 (me).md            ← mes tentatives (avant correction)
│   └── TP1 (correction).md … TP9 (correction).md  ← corrections commentées
│
└── Projet/
    └── facile/                      ← projets de synthèse multi-conteneurs
        ├── facile projet 1.md … facile projet 4.md
        ├── facile projetN (me).md           ← mes tentatives
        └── facile projetN (correction).md   ← corrections
```

---

## 📚 Contenu détaillé

### `cours/facile.md`
Un **cours complet** qui synthétise toutes les notions vues dans les TP, organisées par thème :
1. Lancer, nommer, publier un conteneur
2. Cycle de vie d'un conteneur
3. Logs et exécution dans un conteneur
4. Gestion des images
5. Volumes : persister les données
6. Bind mounts
7. Réseaux Docker
8. Écrire un Dockerfile
9. Bonnes pratiques Dockerfile (cache, `.dockerignore`, multi-stage, non-root, couches)
10. `ARG` vs `ENV`
11. Docker Compose (syntaxe, pièges YAML, `depends_on`, commandes)
12. Précédence des variables d'environnement
13. Publier sur Docker Hub
14. Monitoring et nettoyage
15. Pense-bête de toutes les commandes + les 5 pièges les plus fréquents

### `facile/` — les travaux pratiques
Chaque TP a **trois fichiers** :
- **Enoncé de TPx.md** : la série d'exercices à réaliser.
- **TPx (me).md** : ma tentative personnelle avant correction.
- **TPx (correction).md** : la correction commentée.

Les thèmes abordés, TP par TP :
| TP | Thèmes |
|----|--------|
| TP1 | `run`, `--name`, `-p`, `ps`, `stop`/`rm` |
| TP2 | `logs`, `exec`, gestion des images |
| TP3 | `inspect`, `restart`, `pause`, `cp`, variables d'env, `--memory`, `prune` |
| TP4 | Volumes et réseaux (persistance + communication) |
| TP5–TP9 | Dockerfile, bonnes pratiques, Compose, etc. |
| TP Final | Synthèse complète |

### `Projet/facile/` — projets de synthèse
Des **mini-projets réalistes** qui combinent toutes les commandes dans des architectures multi-conteneurs :
1. **Mini stack web** : app Node.js (Dockerfile) + MySQL, réseau + volume.
2. **Stack Blog** : Nginx + PHP (Dockerfile) + MySQL.
3. **Stack API + Redis** : API Node.js + Redis avec compteur persistant.
4. Projet 4 : synthèse plus poussée.

Chaque projet suit un même schéma pédagogique : réseau → volumes → base de données → Dockerfile de l'app → lancement → vérifications → nettoyage.

---

## 🚀 Progression d'apprentissage

```
TP1 : run, name, port, ps, stop/rm
   ↓
TP2 : logs, exec, images
   ↓
TP3 : inspect, restart, pause, cp, env, memory, prune
   ↓
TP4 : volumes, réseaux (persistance + communication)
   ↓
TP5–TP9 : Dockerfile, bonnes pratiques, Compose
   ↓
Projets : le tout combiné dans des stacks multi-conteneurs
```

---

## 🛠️ Prérequis

- **Docker** installé et configuré (`docker --version`).
- **Docker Compose** (inclus avec Docker Desktop sur Windows/macOS).
- Un terminal (PowerShell, Bash…).

## ▶️ Comment démarrer

Le repository est essentiellement **documentaire** (fichiers `.md`) : il n'y a pas de code applicatif à exécuter directement. Pour en tirer parti :

1. Parcourez `cours/facile.md` pour avoir la vue d'ensemble des notions.
2. Choisissez un TP dans `facile/` et tentez de le résoudre (votre version dans `TPx (me).md`).
3. Comparez avec `TPx (correction).md`.
4. Validez avec les projets de synthèse dans `Projet/facile/`.

> 💡 Exécutez les commandes dans un vrai terminal Docker pour un apprentissage actif.

---

## 👨‍💻 Auteur & objectif

Projet personnel d'**entraînement à Docker** assisté par Claude, avec pour but de :
- mémoriser les commandes Docker essentielles et leurs pièges classiques ;
- pratiquer l'écriture de `Dockerfile` propres et optimisés ;
- comprendre la persistance (volumes) et la communication (réseaux) entre conteneurs ;
- appréhender les architectures multi-conteneurs via Docker Compose.

---

## 📄 Licence

Libre d'utilisation et de réutilisation à des fins d'apprentissage.
