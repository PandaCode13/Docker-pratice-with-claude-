Partie 1 — CMD vs ENTRYPOINT (exercices 1 à 4)

Exercice 1
Écrivez un Dockerfile minimal (basé sur alpine) qui utilise ENTRYPOINT pour toujours exécuter echo "Hello" au démarrage du conteneur, quoi qu'on passe en argument à docker run.

Exercice 2
En partant du Dockerfile de l'exercice 1, ajoutez un CMD qui fournit un argument par défaut ("Docker") à cet ENTRYPOINT, de sorte que le conteneur affiche Hello Docker par défaut, mais que la valeur "Docker" puisse être remplacée si on passe un autre mot à docker run.

Exercice 3
Avec le Dockerfile de l'exercice 2, écrivez la commande docker run qui remplace l'argument par défaut pour afficher Hello Monde à la place.

Exercice 4
En une phrase, expliquez la différence fondamentale entre CMD seul et ENTRYPOINT seul concernant ce qui se passe quand on ajoute une commande après le nom de l'image dans docker run.

Partie 2 — ARG et arguments de build (exercices 5 à 8)

Exercice 5
Dans un Dockerfile, déclarez un argument de build nommé NODE_VERSION avec une valeur par défaut 18, puis utilisez-le dans l'instruction FROM pour construire dynamiquement node:${NODE_VERSION}.

Exercice 6
Écrivez la commande docker build qui construit une image nommée mon-app:1.0 en surchargeant NODE_VERSION avec la valeur 20.

Exercice 7
En une phrase, expliquez la différence principale entre ARG et ENV (disponibilité, persistance dans l'image finale).

Exercice 8
Peut-on accéder à une variable définie avec ARG une fois le conteneur démarré (via docker exec ... env) ? Justifiez votre réponse.

Partie 3 — Registre Docker Hub : tag, login, push, pull (exercices 9 à 13)

Exercice 9
Vous avez une image locale nommée mon-app:1.0. Écrivez la commande pour la taguer en vue de la publier sur Docker Hub sous votre nom d'utilisateur monuser, toujours avec le tag 1.0.

Exercice 10
Écrivez la commande pour vous connecter à Docker Hub en ligne de commande.

Exercice 11
Écrivez la commande pour publier (envoyer) l'image taguée de l'exercice 9 vers Docker Hub.

Exercice 12
Sur une autre machine, écrivez la commande pour récupérer cette image depuis Docker Hub.

Exercice 13
Écrivez la commande pour lister uniquement les images locales dont le nom de repository contient monuser.

Partie 4 — Monitoring : stats et top (exercices 14 à 16)

Exercice 14
Écrivez la commande pour afficher, en temps réel, la consommation CPU et mémoire de tous les conteneurs en cours d'exécution.

Exercice 15
Écrivez la commande pour afficher les statistiques d'un seul conteneur nommé web, sans que l'affichage se rafraîchisse en continu (une seule fois).

Exercice 16
Écrivez la commande pour afficher la liste des processus en cours d'exécution à l'intérieur du conteneur web (équivalent d'un ps mais depuis l'hôte, sans exec).

Partie 5 — Compose multi-fichiers (dev/prod) (exercices 17 à 18)

Exercice 17
Vous avez un fichier docker-compose.yml de base, et un fichier docker-compose.override.yml qui ajoute des options spécifiques au développement (par exemple un volume de bind mount pour le hot-reload). Écrivez la commande docker compose qui utilise automatiquement les deux fichiers combinés (comportement par défaut de Compose).

Exercice 18
Vous avez maintenant un fichier séparé docker-compose.prod.yml (au lieu du .override.yml automatique). Écrivez la commande pour lancer la stack en combinant explicitement docker-compose.yml et docker-compose.prod.yml.

Partie 6 — Précédence des variables d'environnement (exercices 19 à 20)

Exercice 19
Dans un service Compose, vous avez à la fois un fichier .env contenant PORT=3000, et directement dans le docker-compose.yml une ligne environment: - PORT=4000. Quelle valeur de PORT sera effectivement utilisée à l'intérieur du conteneur final ? Justifiez.

Exercice 20
Vous lancez ensuite ce même service avec docker run -e PORT=5000 ... en ligne de commande directe (en dehors de Compose, en supposant que l'image contient déjà une instruction ENV PORT=3000 dans son Dockerfile). Quelle valeur de PORT sera utilisée dans le conteneur ? Classez, du plus prioritaire au moins prioritaire, les trois sources possibles d'une variable d'environnement rencontrées dans ce TP (ENV du Dockerfile, .env/environment de Compose, -e de docker run).