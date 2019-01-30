# The MathWebSearch System

The MathWebSearch System, managed with Docker Compose. 
** Work in Progress **

- Docker Entry Point and Letsencrypt Setup
    - [jwilder/nginx-proxy](https://github.com/jwilder/nginx-proxy) - entry point taking care of https -> http
    - [jrcs/letsencrypt-nginx-proxy-companion](https://github.com/JrCs/docker-letsencrypt-nginx-proxy-companion) - Manages and renews letsencrypt certificates
- MWS System
    - [MathWebSearch/mws](https://github.com/MathWebSearch/mws) - the MWS daemon
    - [MathWebSearch/mws-cron](https://github.com/MathWebSearch/mws-cron) - schedulder for index updates
    - [MathWebSearch/frontend](https://github.com/MathWebSearch/frontend) - a browser frontend to MWS
    - [MathWebSearch/latexml-mws-docker](https://github.com/MathWebSearch/latexml-mws-docker) - latexml backend to allow latex-like queries

## Configuration

This repository supports both a local and production setup. 
All configuration is done via env files. 

These can be interactively generated using the configure script:

```bash
./configure
```

This script re-uses existing configuration if available. 
Furthermore, for a local setup, it is safe to accept all defaults. 

**WARNING:** Always shut down MWS before re-running the configuration script. 

## Starting and Stopping

**WARNING:** Do not attempt to run any of these commands without having gone through the configure script first. 

After having configured appropriatly, you can start the setup as follows:

```bash 
# to start all the containers
docker-compose up -d

# to stop the containers, but keep state
docker-compose down

# to stop all containers and remove all state
docker-compose down -v
```

## Updating the index

The setup expects an MWS index to live within the `index` volume.

For convenience, it is automatically generated from the `harvests` volume using the [mathwebsearch/mws-indexer](https://github.com/MathWebSearch/mws-indexer) image. 
This means that if the harvests volume is seeded with a git repository, `git pull` will be run automatically to fetch new harvests. 

To initialize the `harvests` you can manually start a container with the mounted volume, e.g. `docker run -v mws-system_harvests:/harvests/ -i --rm alpine` and then run something along the lines of:

```bash
# install git
apk add git

# clone into the harvests directory
git clone https://my-harvest-repo/repo.git /harvests/

# and exit
exit
```

`mwscron` is used to automatically run the indexing every day at midnight. 
To trigger indexing manually, run:

```bash
    docker-compose exec mwscron /mws-cron --trigger
```

to update the index.

## License

Licensed under GPL 3.0. 
