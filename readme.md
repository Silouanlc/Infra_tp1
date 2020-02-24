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



Dockerfile ( dans le webserver) :
~~~~
FROM alpine:latest

RUN apk add python3 && apk add py-pip

COPY ./app/requirements /app/requirements

WORKDIR /app

RUN pip install -r requirements

COPY ./pythonapp /pythonapp

ENTRYPOINT [ "python" ]

CMD ["app.py"]

~~~~

docker-compose.yml (à la racine):
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
🌞 "swarmiser" l'application Python du TP1:
~~~
[vagrant@vm2 ~]$ sudo docker build - < Dockerfile 
Sending build context to Docker daemon  2.048kB
Step 1/4 : FROM alpine:latest
 ---> e7d92cdc71fe
Step 2/4 : RUN apk update && apk add python3
 ---> Running in 3969c9a126ca
fetch http://dl-cdn.alpinelinux.org/alpine/v3.11/main/x86_64/APKINDEX.tar.gz
fetch http://dl-cdn.alpinelinux.org/alpine/v3.11/community/x86_64/APKINDEX.tar.gz
v3.11.3-75-gbcab687d4f [http://dl-cdn.alpinelinux.org/alpine/v3.11/main]
v3.11.3-79-gcdba3c9b8f [http://dl-cdn.alpinelinux.org/alpine/v3.11/community]
OK: 11266 distinct packages available
(1/11) Installing libbz2 (1.0.8-r1)
(2/11) Installing expat (2.2.9-r1)
(3/11) Installing libffi (3.2.1-r6)
(4/11) Installing gdbm (1.13-r1)
(5/11) Installing xz-libs (5.2.4-r0)
(6/11) Installing ncurses-terminfo-base (6.1_p20191130-r0)
(7/11) Installing ncurses-terminfo (6.1_p20191130-r0)
(8/11) Installing ncurses-libs (6.1_p20191130-r0)
(9/11) Installing readline (8.0.1-r0)
(10/11) Installing sqlite-libs (3.30.1-r1)
(11/11) Installing python3 (3.8.1-r0)
Executing busybox-1.31.1-r9.trigger
OK: 71 MiB in 25 packages
Removing intermediate container 3969c9a126ca
 ---> a6f5d78dac46
Step 3/4 : CMD ["python3", "-m", "http.server", "8888"]
 ---> Running in 7f242f39454c
Removing intermediate container 7f242f39454c
 ---> 21c2c4549dfb
Step 4/4 : EXPOSE 8888
 ---> Running in b6cd8b38695a
Removing intermediate container b6cd8b38695a
 ---> 0a586d5d4b23
Successfully built 0a586d5d4b23
~~~
🌞 explorer l'application et les fonctionnalités de Swarm :
