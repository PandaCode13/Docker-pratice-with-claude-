PARTIE 1 : CMD vs ENTRYPOINT (Exercice 1 à 4)

Exercice 1 : 

Dans le fichier Dockerfile

FROM alpine
ENTRYPOINT [echo, "Hello"]

exercice 2 : 

FROM alpine 
ENTRYPOINT [echo, "Hello"]
CMD ["echo", "Hello Docker"]

Exercice 3 : 

docker run mon-image Monde

Exercice 4 : 

