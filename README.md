# The MathWebSearch System

The MathWebSearch System, managed with Docker Compose. 
** Work in Progress **

- Docker Entry Point and Letsencrypt Setup
    - [jwilder/nginx-proxy](https://github.com/jwilder/nginx-proxy) - entry point taking care of https -> http
    - [jrcs/letsencrypt-nginx-proxy-companion](https://github.com/JrCs/docker-letsencrypt-nginx-proxy-companion) - Manages and renews letsencrypt certificates
- MWS System
    - [MathWebSearch/mws](https://github.com/MathWebSearch/mws) - the MWS daemon
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

Run

```bash
    docker run -t -i --rm -e MWS_DOCKER_LABEL="org.mathweb.mwsd" -v /path/to/harvests:/data/ -v index:/index/ -v /var/run/docker.sock:/var/run/docker.sock mathwebsearch/mws-updater
```

to update the index.

## License

Licensed under GPL 3.0. 
