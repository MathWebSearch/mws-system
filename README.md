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

**WARNING:** Do not attempt to run any of these commands without having gone through the configure script and having bootstrapped (see below) first. 

After having configured appropriatly, you can start the setup as follows:

```bash 
# to start all the containers
docker-compose up -d

# to stop the containers, but keep state
docker-compose down

# to stop all containers and remove all state
docker-compose down -v
```

## Index Bootstrap && Updating

The setup expects an MWS index to live within the `index` volume and an MWS harvest to live within the `harvests` volume or startup may fail. 

Bootstrapping this has to be done in three steps, making use of the [mathwebsearch/mws-indexer](https://github.com/MathWebSearch/mws-indexer) indexer. 

```bash

# First create the index and harvests volumes
docker volume create index
docker volume create harvests

# Then initialize data inside the harvests volume, e.g. using git
docker run -t -i --rm -v harvests:/harvests/ alpine 'apk add git; git clone https://my.harvest.server.com/harvests.git /harvests/'

# Run an initial indexer
docker run -t -i --rm -v harvests:/data/ -v index:/index/ mathwebsearch/mws-indexer
```

For convenience, further indexes are automatically updated using the `mwscron` service. 
This makes use of the same indexing image. 
If the harvests directory is a git repository, this image will also automatically run `git pull`. 

The index is automatically re-generated every day at midnight (GMT).
However re-indexing can also be triggered manually using the following command:

```bash
    docker-compose exec mwscron /mws-cron --trigger
```

## License

Licensed under GPL 3.0. 
