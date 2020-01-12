## 2
-   🌞 créer une image qui lance un serveur web python. L'image doit :
    -   se baser sur alpine (clause `FROM`)
    -   contenir `python3` (clause `RUN`)
    -   utiliser la clause `EXPOSE` afin d'être plus explicite
        -   **NB : la clause EXPOSE ne partage aucun port**, c'est simplement utilisé pour préciser de façon explicite que ce conteneur écoutera sur un port donné
        -   cela permet à quelqu'un qui analyse votre image de savoir rapidement quel port sera utilisé par un conteneur donné
    -   contenir une clause `WORKDIR` afin de spécifier un répertoire de travail (les commandes suivantes seront lancées depuis ce répertoire)
    -   utiliser `COPY` pour récupérer un fichier à partager à l'aide du serveur HTTP
    -   lancer la commande `python3 -m http.server 8888` (clause `CMD`)
        -   cela lance un serveur web qui écoute sur le port TCP 8888 de toutes les interfaces du conteneur
  
~~~~
`[root@localhost l2]# cat Dockerfile`
`FROM alpine:latest`
`COPY . /app`
`WORKDIR /app`
`RUN apk update && apk add python3`
`Expose 8888`
`COPY index.html .`
`CMD ["python3", "-m", "http.server", "8888"]`
~~~~

## 3
🌞 Modifier la configuration du démon Docker :

-   modifier le socket utilisé pour la communication avec le démon Docker
    -   trouvez le path du socket UNIX utilisé par défaut (c'est un fichier `docker.sock`)
    :`/var/run/docker.sock` 
    
- utiliser un socket TCP (port TCP) à la place
	-  autrement dit, il faut que votre démon Docker écoute sur un IP:PORT plutôt que sur le path d'un socket UNIX local
~~~~
`: dockerd -H tcp://192.168.202.3`
~~~~

## Write your own

🌞 Ecrire un `docker-compose-v1.yml` qui permet de :

-   lancer votre image de serveur web Python créée en [2.](#cr%c3%a9ation-dimage)
-   partage le port TCP du conteneur sur l'hôte
-   faire en sorte que le conteneur soit build automatiquement si ce n'est pas fait
~~~~
    version: '3.7'
    services:
     node:
     build: .
     restart: on-failure
     ports:
     - "8888:8888"

~~~~

##Conteneuriser une application donnée

Ici on se rapproche d'un cas d'utilisation réel : je vous mets une application sur les bras et vous devez la conteneuriser.
L'application :

codée en python3

les sources sont dans ici
n'hésitez pas à cloner mon repo pour copier directement les fichiers


nécessite des librairies installables avec pip

pip install -r <FICHIER>


a besoin d'un Redis pour fonctionner

il doit être joignable sur le nom db (port par défaut (6379/TCP))



🌞 Vous devez :

construire une image qui

contient python3

contient l'application et ses dépendances
lance l'application au démarrage du conteneur


écrire un docker-compose.yml

contient l'application
contient un Redis

utilise l'image de library

a un alias db



contient un NGINX

reverse proxy HTTPS vers l'application Web
a son port 443 exposé


Dockerfile:
~~~~

FROM alpine:latest

RUN apk add py-pip && apk add python3

WORKDIR /app

RUN pip install -r requirements

ENTRYPOINT [ "python" ]

CMD ["app.py"]
~~~~

docker-compose.yml:
~~~~

version: '3.7'

services:
  web:
    build: ./webserver/
    networks:
      internal:
        aliases:
          - python-app
    depends_on:
      - redis
      
  redis:
    image: redis
    networks:
      internal:
        aliases:
          - db
    expose:
      - "6379"
      
  nginx:
    image: nginx
    networks:
      - internal
    volumes:
      - ./nginx/conf.d:/etc/nginx/conf.d
      - ./nginx/certs:/certs
    depends_on:
      - web
      - redis
    ports:
      - "443:443"



networks:
  internal:

~~~~





