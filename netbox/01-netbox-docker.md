# Installing docker
<pre>
basondole@netbox:~/docker/netbox-docker$ sudo apt update
basondole@netbox:~/docker/netbox-docker$ sudo apt install docker.io
</pre>

### Confirm the version and enable the service
<pre>
basondole@netbox:~/docker/netbox-docker$ docker --version
Docker version 18.09.7, build 2d0083d
basondole@netbox:~/docker/netbox-docker$ sudo systemctl start docker
basondole@netbox:~/docker/netbox-docker$ sudo systemctl enable docker
</pre>

### Add current user to docker group
<pre>
basondole@netbox:~/docker$ sudo usermod -aG docker $USER
basondole@netbox:~/docker$ exit
</pre>
> After adding user to the group you must log out and log back in

### Install docker-compose
<pre>
basondole@netbox:~$ sudo curl -L "https://github.com/docker/compose/releases/download/1.24.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   617    0   617    0     0    406      0 --:--:--  0:00:01 --:--:--   406
100 15.4M  100 15.4M    0     0  2591k      0  0:00:06  0:00:06 --:--:-- 3498k
basondole@netbox:~$ sudo chmod +x /usr/local/bin/docker-compose
</pre>

### Clone the netbox-docker repo
<pre>
basondole@netbox:~$ git clone -b master https://github.com/netbox-community/netbox-docker.git
Cloning into 'netbox-docker'...
remote: Enumerating objects: 5, done.
remote: Counting objects: 100% (5/5), done.
remote: Compressing objects: 100% (4/4), done.
remote: Total 1413 (delta 0), reused 0 (delta 0), pack-reused 1408
Receiving objects: 100% (1413/1413), 400.98 KiB | 1.00 MiB/s, done.
Resolving deltas: 100% (782/782), done.
basondole@netbox:~$
</pre>

### Start the docker container
<pre>
basondole@netbox:~$ cd netbox-docker/
basondole@netbox:~/netbox-docker$ ls
build-all.sh       build.sh       docker-compose.yml  env           LICENSE    scripts
build-branches.sh  configuration  Dockerfile          hooks         README.md  startup_scripts
build-latest.sh    docker         DOCKER_HUB.md       initializers  reports    VERSION

basondole@netbox:~/netbox-docker$ docker-compose pull
Pulling postgres      ... done
Pulling redis         ... done
Pulling netbox-worker ... done
Pulling netbox        ... done
Pulling nginx         ... done

basondole@netbox:~/netbox-docker$ docker-compose up -d
Recreating netboxdocker_redis_1    ... done
Recreating netboxdocker_postgres_1      ... done
Recreating netboxdocker_netbox-worker_1 ... done
Recreating netboxdocker_nginx_1         ... done
basondole@netbox:~/netbox-docker$
</pre>

### How to access Netbox 
<pre>
basondole@netbox:~/netbox-docker$ echo "http://$(docker-compose port nginx 8080)/)/"
http://0.0.0.0:32768/
basondole@netbox:~/netbox-docker$
</pre>

The above command will print out the exact port you should use to access Netbox
It will take around two to five minutes before Netbox becomes available.
In the case of my example, the port to be used is `32768`.


So point a browser to `http://192.168.56.20:32768` where `192.168.56.20` is the IP of my server.
If the page doesn't appear, wait for a few more minutes for the service to become available and try again.  

![netbox](https://user-images.githubusercontent.com/50369643/68690723-94970000-0583-11ea-949a-8c4a3022c86e.png)

Click Login and authenticate with the user admin and the password specified in netbox.env file
<pre>
basondole@netbox:~/netbox-docker$ cat env/netbox.env
CORS_ORIGIN_ALLOW_ALL=True
DB_NAME=netbox
DB_USER=netbox
DB_PASSWORD=J5brHrAXFLQSif0K
DB_HOST=postgres
EMAIL_SERVER=localhost
EMAIL_PORT=25
EMAIL_USERNAME=netbox
EMAIL_PASSWORD=
EMAIL_TIMEOUT=5
EMAIL_FROM=netbox@bar.com
MEDIA_ROOT=/opt/netbox/netbox/media
NAPALM_USERNAME=
NAPALM_PASSWORD=
NAPALM_TIMEOUT=10
MAX_PAGE_SIZE=1000
REDIS_HOST=redis
REDIS_PASSWORD=H733Kdjndks81
REDIS_DATABASE=0
REDIS_CACHE_DATABASE=1
REDIS_SSL=false
SECRET_KEY=r8OwDznj!!dci#P9ghmRfdu1Ysxm0AiPeDCQhKE+N_rClfWNj
SKIP_STARTUP_SCRIPTS=false
SKIP_SUPERUSER=false
SUPERUSER_NAME=admin
SUPERUSER_EMAIL=admin@example.com
SUPERUSER_PASSWORD=admin
SUPERUSER_API_TOKEN=0123456789abcdef0123456789abcdef01234567
WEBHOOKS_ENABLED=true
basondole@netbox:~/netbox-docker$
</pre>

