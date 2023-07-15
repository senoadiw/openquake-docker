# openquake-docker
Run OpenQuake LTS (v3.16 as of February 2023) on Docker with auto generated Let's Encrypt SSL certificate.

Based on the following Docker images:
* https://github.com/gem/oq-engine/tree/master/docker
* https://github.com/nginx-proxy/nginx-proxy
* https://github.com/nginx-proxy/acme-companion

## Requirements
* Docker (and Docker Compose) installed
  * for Windows 10 up install Docker Desktop: https://docs.docker.com/desktop/install/windows-install/
* for Linux: the user must belong the docker group
  * `sudo usermod -aG docker ${USER}`
* for production: a domain name pointed to the target server's IP address

## How to run (for testing/development)
```
cd /path/to/working/dir
git clone https://github.com/senoadiw/openquake-docker.git
cd openquake-docker

# copy the sample environment files
cp .env.dev.sample .env.dev

# bring up the containers
docker compose -f docker-compose.dev.yml up -d

# check the logs and wait a few minutes for the containers to initialize
docker compose -f docker-compose.dev.yml logs -f

# check the container status
docker ps

# access OpenQuake at http://localhost:8800 or http://the.server.ip.address:8800

# login with the admin user details in .env.dev

# to access oq command
docker compose -f docker-compose.dev.yml exec -it openquake-engine /bin/bash
oq --version

# to view the container logs
docker compose -f docker-compose.dev.yml logs openquake-engine -f

# to stop the container
docker compose -f docker-compose.dev.yml stop

# to start the container
docker compose -f docker-compose.dev.yml start

# to delete the container (and all calculation data!)
docker compose -f docker-compose.dev.yml down

# the OpenQuake version is fixed to 3.16 (https://hub.docker.com/r/openquake/engine/tags?page=1&name=3.16)
# do the following to pull image version 3.16.x with the latest updates:
docker compose -f docker-compose.dev.yml stop
# list the images
docker images
# force delete the openquake/engine image by image id abcdefghijkl (example)
docker rmi -f abcdefghijkl
# pull the latest image
docker compose -f docker-compose.dev.yml up -d

# done!
```

## How to run (for production)
SSH to the target server and run:
```
git clone https://github.com/senoadiw/openquake-docker.git
cd openquake-docker

# for production installation, copy the sample environment files
cp .env.prod.sample .env.prod

# configure openquake environment variables
nano .env.prod
  # edit OQ_ADMIN_LOGIN, OQ_ADMIN_PASSWORD, OQ_ADMIN_EMAIL, VIRTUAL_HOST,
  # LETSENCRYPT_HOST, and LETSENCRYPT_EMAIL in .env.prod
  # optional: uncomment and edit WEBUI_PATHPREFIX (will change URL from https://oq.domain.com/engine to https://oq.domain.com/WEBUI_PATHPREFIX/engine)

# optional: configure openquake container to always start on server boot
nano docker-compose.prod.yml
  # uncomment "restart: always" below "image: openquake/engine:3.16"

# bring up the containers
docker compose -f docker-compose.prod.yml up -d

# check the logs and wait a few minutes for the containers to initialize
docker compose -f docker-compose.prod.yml logs -f

# access OpenQuake at the configured domain (example: https://oq.domain.com)

# login with the admin user details in .env.prod

# to access oq command
docker compose -f docker-compose.prod.yml exec -it openquake-engine /bin/bash
oq --version

# to stop the container
docker compose -f docker-compose.prod.yml stop

# to start the container
docker compose -f docker-compose.prod.yml start

# to delete the container (and all calculation data!)
docker compose -f docker-compose.prod.yml down

# done!
```
