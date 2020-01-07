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


redis:
    container_name: redis
    image: redis
    ports:
      - "6379:6379"
    volumes:
      - ../data/redis:/data
    restart: always
	
------------------------------

app.py
	
import redis
import time

time.sleep(5)

r = redis.StrictRedis(host='db', port=6379, db=0)

r.set('test', 'working')

i = r.get('test')
o = i.decode("utf-8")

print(o)


from flask import Flask, request, render_template
app = Flask(__name__)

@app.route('/')
@app.route('/index')
def index():
    return render_template('index.html',
                           title='Home')

@app.route('/add', methods=['POST', 'GET'])
def add():
    if request.method == 'POST':
        r.set(request.form['key'], request.form['value'])

    return 'Successfully added key ' + request.form['key']

@app.route('/get', methods=['POST'])
def get():
    try:
        if request.method == 'POST':
            keyBytes = r.get(request.form['key'])
            key = keyBytes.decode('utf-8')
        return 'You asked about key ' + request.form['key'] + ". Value : " + key
    except:
        return 'Key ' + request.form['key'] + " does not exist."


if __name__ == '__main__':
    app.run(host='0.0.0.0')

---------------------------------------------

version: “3”
services:
web:
image: nginx
volumes:
- /opt/nginx:/etc/nginx
- /opt/nginx/conf.d:/etc/nginx/conf.d
- /opt/www:/opt/www
deploy:
replicas: 2
restart_policy:
condition: on-failure
ports:
- “80:80”
- “443:443”
networks:
- webnet
networks:
webnet:
Please give me some advice, thank you very much.

---------------------------------------------------
