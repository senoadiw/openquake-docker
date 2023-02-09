# openquake-docker
Run OpenQuake on Docker with auto generated Let's Encrypt SSL certificate.

## Requirements
* Docker (and Docker Compose) installed
* for Linux: the user must belong the docker group
  * `sudo usermod -aG docker ${USER}`
* for production: a domain name pointed to the target server's IP address

## How to run
SSH to the target server and run:
```
git clone https://github.com/senoadiw/openquake-docker.git
cd openquake-docker

# for production installation, copy the sample environment files
cp .env.prod.sample .env.prod

# edit OQ_ADMIN_LOGIN, OQ_ADMIN_PASSWORD, OQ_ADMIN_EMAIL, VIRTUAL_HOST,
# LETSENCRYPT_HOST, and LETSENCRYPT_EMAIL in .env.prod
nano .env.prod

# bring up the containers
docker compose -f docker-compose.prod.yml up -d

# check the logs and wait a few minutes for the containers to initialize
docker compose -f docker-compose.prod.yml logs -f

# access OpenQuake at the configured domain (example: https://oq.domain.com)

# to access oq command
docker compose -f docker-compose.prod.yml exec -it openquake-engine /bin/bash
oq --version

# done!
```
