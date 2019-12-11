# Using Docker Compose

Simplify container-based applications by writing your *docker-compose.yml* and then ... get your multi-container application up and running.

## Tips

In your env file set the **DOCKER_PROJECT_NAME** explicitly to avoid "mess":

```
# 
COMPOSE_PROJECT_NAME=<my-name>
```

## When in doubt
```
docker-compose help
```

## Sample docker-compose.yml
```
version: '3'

services:
    redis:
      image: 'redis:3.2-alpine'
      ports:
        - '6379:6379'
      volumes:
        - 'redis:/data'    
    web:
      build: '.'
      depends_on:
          - 'redis'
## We can also create a repository for the built image
#      image: 'marioscalas/web:1.0'
## We can map single environment variables but it is better to externalize files
#  environment:
#        FLASK_DEBUG: 'true'
## This is the way
      env_file:
        - '.env'
      ports:
        - '5001:5000'
## No more tricks for mounting volumes on windows CLI :)
      volumes:
        - '.:/app'
## Define a volume 'redis' with default options (look at the docs for configurations, like read-only)
volumes:
  redis: {}
```

## Build

Build all images specified in the *docker-compose.yml* file:
```
docker-compose build

docker image ls
REPOSITORY          TAG                 IMAGE ID            CREATED              SIZE
web2_web            latest              df63546d2670        About a minute ago   110MB
python              3.7.5-alpine        8922d588eec6        3 weeks ago          98.4MB
```

Iamges that are not built but are fetched from remote:

```
docker-compose pull

docker image ls
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
web2_web            latest              df63546d2670        3 minutes ago       110MB
python              3.7.5-alpine        8922d588eec6        3 weeks ago         98.4MB
redis               3.2-alpine          6e94a98d3442        14 months ago       22.9MB
```

## Run

Now run the container:
```
docker-compose up
```

```
docker-compose restart
docker-compose restart redis
```

## Build/Pull and run in a single step

```
docker-compose up --build 
```

Note: you can add *-d' to run in daemon mode.
```
docker-compose up --build -d
docker-compose logs -f

```

## Execute commands on containers

```
docker-compose exec web ls /
```
