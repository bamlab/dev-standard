# [MO] Setup HTTPS on your docker environment

## Owner: [Sammy Teillet](https://github.com/samox)

## Prerequisites

* [ ] You have a staging environment with docker
* [ ] You can ssh to the server
* [ ] Make sure you have the docker rights `sudo usermod -aG docker $YOUR_USER_NAME`
* [ ] Your server has a domain name (not only an IP)


## Steps

### Install the nginx-proxy companion

- Connect to your server `ssh user@your.domain`
- Clone the nginx-proxy-companion [project](https://github.com/evertramos/docker-compose-letsencrypt-nginx-proxy-companion) on the server at the root of the server.

```bash
git clone git@github.com:evertramos/docker-compose-letsencrypt-nginx-proxy-companion.git
```

- Create a `.env` file

```bash
cd docker-compose-letsencrypt-nginx-proxy-companion
cp ./.env.sample .env
```

- Set the `NGINX_FILES_PATH=/srv/nginx/data` in the `.env`
    - `vim ./.env`
    - line 41 replace `NGINX_FILES_PATH=/srv/nginx/data`(or a different path if you prefer)

{% hint style='success' %} **CHECK**

Try to launch the companion by running:

```bash
./start.sh
```

You should have the following error because the port 80 is already used by your docker:

```
ERROR: for nginx-web  Cannot start service nginx-web: driver failed programming external connectivity on endpoint nginx-web (4c0105fe57d370c99c0a143c967d1b8737006a4138618e1defebc4bab4e42d11): Bind for 0.0.0.0:80 failed: port is already allocated
```

![](./docker-nginx-companion-error.png)

{% endhint %}

### Configure your project to use the companion

- Remove the binding 80 port command, but expose it

```diff
version: '3'
services: 
  your-web-app: #It should contain port: "80:80"
    # ... 
-   ports:
-     - "80:80"
+   expose:
+     - 80
```

- Configure the app to use the network created by the companion (`webproxy` is the default name)

```diff
version: '3'
services: 
    # ... 

+networks:
+  default:
+     external:
+        name: webproxy
```

{% hint style='info' %} **GO FURTHER**

https://blog.docker.com/2016/12/understanding-docker-networking-drivers-use-cases/

{% endhint %}

{% hint style='info' %} **YOUR DOCKER DOES NOT USE THE PORT 80**

You can set the `VIRTUAL_PORT` in your docker configuration

{% endhint %}

- In your project set 3 environment variable : `VIRTUAL_HOST`, `LETSENCRYPT_HOST`, `LETSENCRYPT_EMAIL`. The email will be used by _Letsencrypt_ to notify you if the certificate expire.There are 2 ways:
    - In the docker-compose file
    - In your prod.env file that is read by your Dockerfile.


{% hint style='info' %} **RECOMENDED WAY**

Update the .env file of your web-app docker

{% endhint %}

- In the `./env/prod.env` add the following:

```diff
#... other env variable
+ VIRTUAL_HOST=my.domain.cloud.bam.tech
+ LETSENCRYPT_HOST=my.domain.cloud.bam.tech
+ LETSENCRYPT_EMAIL=your@email.com
```

{% hint style='warning' %} **OTHER solution**

If you have no .env file you an also Update the docker-compose-prod file

```diff
version: '3'
services: 
  your-web-app: #It should contain port: "80:80"
    # ... 
    environment:
+      - VIRTUAL_HOST=my.domain.cloud.bam.tech
+      - LETSENCRYPT_HOST=my.domain.cloud.bam.tech
+      - LETSENCRYPT_EMAIL=your@email.com
```

{% endhint %}


### Make the switch

{% hint style='danger' %} **BUSINESS INTERRUPTION**

You will have to shut down your docker (so the port 80 is available), so during this step your domain  won't be accessible.

{% endhint %}

- Cut your app docker:

```bash
cd your-project-directory
docker-compose -f docker-compose-prod.yml down
```

- Start the companion (go to the companion directory): 

```bash
cd ../docker-compose-letsencrypt-nginx-proxy-companion
./start.sh
```

- Launch your project docker again:

```bash
cd -
docker-compose -f docker-compose-prod.yml up -d
```

{% hint style='success' %} **CHECK**

- Check the validity of your domain, go to https://your.domain
- Go [there](https://www.ssllabs.com/ssltest/) and check your domain. Usefull tip: go to the __Handshake Simulation__ section and check the supported devices.

{% endhint %}

## Troubeshooting

- https://your.domain does not respond:
    - Shut down your dockers by running `docker-compose -f the-docker-compose-file.yml stop` in both your project folder and the nginx companion folder
    - Check: if you do `docker ps` there are no docker up
    - Restart the companion by running `start.sh` in the companion folder
    - Restart your project
