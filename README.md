# docker-carto
Configuración de Docker para contener Carto como herramienta SIG

## Build Contenedor
docker build -t sebaxtian/docker-carto:1.0.0 .

## Config Hostname
The Carto instance has been configured with the hostname carto.localhost, this means the web browser and web server need to be able to resolve carto.localhost to an IP adress of the machine where the web server is running. This can be done by adding carto.localhost alias to your hosts file. For example

sudo sh -c 'echo 127.0.1.1 CARTO_HOSTNAME >> /etc/hosts'

Go to http://CARTO_HOSTNAME/login

username = dev
password = pass1234

## Persistent data
To persist the PostgreSQL data, the PostGreSQL data dir (/var/lib/postgresql) must be persisted outside the Cartodb Docker container.

The PostgreSQL data dir is filled during the building of this Docker image and must be copied to the local filesystem and then the container must be started with the local copy volume mounted.

docker create --name carto_pgdata sverhoeven/cartodb

### Change to directory to save the Postgresql data dir (carto_pgdata) of the Carto image
docker cp carto_pgdata:/var/lib/postgresql $PWD/data/carto_pgdata
docker rm -f carto_pgdata

## Run Contenedor
docker run --name docker-carto --env-file $PWD/.env -v $PWD/data/carto_pgdata:/var/lib/postgresql -p 80:80 -d sebaxtian/docker-carto:1.0.0

## Config Geocoder
The external geocoders like heremaps, mapbox, mapzen or tomtom have dummy api keys and do not work. No attempts have been made or will be made in this Docker image to get the external geocoders to work.
The internal geocoder is configured, but contains no data inside the image.
To fill the internal geocoder run

docker exec -ti docker-carto bash -c /cartodb/script/fill_geocoder.sh

## Restart Contenedor
docker restart docker-carto

## Ver logs de Contenedor
docker logs docker-carto

## Shell en Contenedor
docker exec -it docker-carto bash

## Eliminar Contenedor
docker rm docker-carto

## Eliminar Image de Contenedor
docker image rm sebaxtian/docker-carto:1.0.0
