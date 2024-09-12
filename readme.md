## nginx certbot startup

Setup nginx with an SSL certificate and custom configuration. Based on [Jonas Alfredsson's docker-nginx-certbot repo](https://github.com/JonasAlfredsson/docker-nginx-certbot).

### 0. Create this relative folder path

Folders will be created in this path in order to save certs etc.
`mkdir -p instance/nginx`

### 1. Create an external docker network

Create an external docker bridge network in order to have the ability to _possibly_ connect multiple _docker composed_ services running on the same machine under the same network topology. 
`docker network create -d bridge container_network`

### 2. Modify these values before running
In `.env` modify:
- NGINX_CERTBOT_VERSION -> to the latest stable version ( big tag name == stable version )

In `nginx-certbot.env` modify:
- CERTBOT_EMAIL -> your email address ( will be used for updates regarding your certs )

In `app.conf` modify:
- you.domain.com to your domain

### 3. Dry-Run settings to make sure staff works
In `nginx-certbot.env` modify:
- STAGING=1
- DEBUG=1

Then, on the __root__ project folder run `docker compose up`.
If all ok, `Ctrl+C` and run 

```bash
docker compose down --remove-orphans
rm -rf ./instance/nginx/* # remove testing certs etc.
```

and continue to the next step

### 4. Finalize setup and run 
In `nginx-certbot.env` modify:
- STAGING=0
- DEBUG=0

Then, on the root folder run `docker compose up -d`.
Navigate to https://your.domain.com, you should be able to see the appropriate message of the default config.
In case of error, check logs using `docker logs <container_name>`

### 5. Change `app.conf` to suit your needs
- ALWAYS Copy the previous app.conf before making changes, store it in case of fckups
- After changes enter `docker exec -it <container_name> /bin/bash` the nginx container and run `service nginx --full-restart`.