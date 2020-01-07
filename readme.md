TP 1 









[toor@localhost root]$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
8e5667f1a413        alpine              "sleep 999"         9 minutes ago       Up 9 minutes                            vigilant_chatterjee
[toor@localhost root]$ sudo docker exec -it 8e5667f1a413 sh
/ #         


----------------------

suppression stop 

[toor@localhost root]$ sudo docker stop 8e5667f1a413
8e5667f1a413
[toor@localhost root]$ sudo docker rm 8e5667f1a413
8e5667f1a413
[toor@localhost root]$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES


------------------------------

Welcome to nginx!

If you see this page, the nginx web server is successfully installed and working. Further configuration is required.

For online documentation and support please refer to nginx.org.
Commercial support is available at nginx.com.

Thank you for using nginx.


------------------------------

apache2.2

[toor@localhost root]$ sudo docker pull httpd:2.2
[sudo] password for toor:
2.2: Pulling from library/httpd
f49cf87b52c1: Pull complete                                                                                             24b1e09cbcb7: Pull complete                                                                                             8a4e0d64e915: Pull complete                                                                                             bcbe0eb4ca51: Pull complete                                                                                             16e370c15d38: Pull complete                                                                                             Digest: sha256:9784d70c8ea466fabd52b0bc8cde84980324f9612380d22fbad2151df9a430eb
Status: Downloaded newer image for httpd:2.2
docker.io/library/httpd:2.2
[toor@localhost root]$   


[toor@localhost root]$ docker ps
CONTAINER ID        IMAGE               COMMAND              CREATED             STATUS              PORTS                   NAMES
a81466552c7b        httpd:2.2           "httpd-foreground"   16 seconds ago      Up 14 seconds       0.0.0.0:32771->80/tcp   distracted_jackson
[toor@localhost root]$         
-----------------------------
