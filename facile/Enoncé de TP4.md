Exercice 1 — Créer un volume
Écrivez la commande pour créer un volume Docker nommé mes_donnees.

Exercice 2 — Lister les volumes
Écrivez la commande pour afficher la liste de tous les volumes présents sur votre machine.

Exercice 3 — Utiliser un volume dans un conteneur
Écrivez une commande docker run qui lance un conteneur à partir de l'image mysql en mode détaché, en montant le volume mes_donnees sur le chemin /var/lib/mysql à l'intérieur du conteneur.

Exercice 4 — Inspecter un volume
Écrivez la commande pour afficher les détails (au format JSON) du volume mes_donnees.

Exercice 5 — Supprimer un volume
Écrivez la commande pour supprimer le volume mes_donnees (attention : aucun conteneur ne doit l'utiliser).

Exercice 6 — Bind mount (montage local)
Écrivez une commande docker run qui lance un conteneur nginx en mode détaché, en montant le dossier local /home/user/site sur /usr/share/nginx/html à l'intérieur du conteneur (montage direct depuis votre système de fichiers, pas un volume nommé).

Exercice 7 — Créer un réseau
Écrivez la commande pour créer un réseau Docker nommé mon_reseau.

Exercice 8 — Lister les réseaux
Écrivez la commande pour afficher la liste de tous les réseaux Docker disponibles.

Exercice 9 — Lancer un conteneur sur un réseau spécifique
Écrivez une commande docker run qui lance un conteneur nginx en mode détaché, connecté au réseau mon_reseau.

Exercice 10 — Supprimer un réseau
Écrivez la commande pour supprimer le réseau mon_reseau (attention : aucun conteneur ne doit y être connecté).