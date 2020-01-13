# Scrapbook

The almost-always useful stuff!

**Warning**: in this document the term “image-name” is used instead of “repository-name” 

## Build

### Create a docker image tagged ‘web1’
```
docker image build -t <image-name>:<image-tag> .
```
Example:
```
docker image build -t web1:1.0 .
```
### Show metadata about the image
```
docker image inspect web1
```
### Show the docker images available locally
```
docker image ls
```
### Remove and image from cache

You can do in two ways:
1. **Delete by repository name and tag**: ``docker image rm <image-name>:<image-tag>``
2. **Delete by image id**: ``docker image rm <image-id-first-4-digits>``

```
docker image rm web1:1.0
```
* '-f' forces the image removal, even if it is not dangling (unused) 
```
docker image rm -f 29a4

PS C:\src\docker-fundamentals\src\06-docker-in-the-real-world\03-creating-a-dockerfile-part-1> docker image ls
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
web1                latest              29a488fd263e        2 minutes ago       73.1MB
marioscalas/web1    latest              29a488fd263e        2 minutes ago       73.1MB
```

## Publishing an image to a remote registry (e.g., Docker Hub)
Log into repository:
```
docker login
```

```
docker image tag web1 marioscalas/web1:latest
```
(In this case 'marioscalas/web1' is the repository name)

Push image to remote registry (the one we logged into)
```
docker image push marioscalas/web1:latest
```

We can delete the image and then pull it back
```
docker image rm marioscalas/web1:latest 
docker image pull marioscalas/web1:latest
```

## Renaming / Retagging an image
Use *docker tag* to create a new tag for the image and remove the unwanted one:
```
docker image tag <FROM_TAG:VERSION> <TO_TAG:VERSION>

docker image tag web1:latest marioscalas/web1:latest
docker image rm marioscalas/web1:latest
```

## Run a container

### Starting
```
docker container ls
docker container ls -a
```

Start a container in interactive mode (colored terminal, CTRL+C is working, ...), bind <LOCAL-PORT:CONTAINER-PORT> lists (-p) 
and the environment variable used by the Flask web framework to know which app to run (we can use several '-e' to pass more variables)
--rm removes the container after being stopped
--name sets a name that we can use in the following commands (instead of the default ones)
-d runs the container in daemon mode

Basic:
```
docker container run -it -p 5000:5000 -e FLASK_APP=app.py web1:latest 
```

Super smart:
```
docker container run -it --rm --name web1 -p 5000:5000 -e FLASK_APP=app.py -e FLASK_DEBUG=1 -d web1:latest 
```

### Mounting external volumes
Use the *-v <LOCAL-PATH:CONTAINER-PATH>*

Note: it doesn't seem to like the symlink paths so I've to resort to [this](https://stackoverflow.com/a/46750124/522427]).
```
docker container run -it --rm --name web1 -p 5000:5000 -e FLASK_APP=app.py -e FLASK_DEBUG=1 -d  -v $(readlink -f $PWD):/app web1:latest
```

## Run multiple containers
```
docker container run -it --rm --name web1_2 -p 5000 -e FLASK_APP=app.py -d web1:latest 
```

Auto-respawning container (restarts automatically if the docker daemon restarts)
```
docker container run -it --name web1_3 -p 5000 -e FLASK_APP=app.py -d --restart on-failure  web1:latest
```

## Stopping
docker container stop <CONTAINER_NAME_OR_ID>

Note: stopped container are not cleaned up so we have either to do this manually or just provide a flag when starting it:
```
docker container stop beautiful_heyrovsky
```

## Monitoring container logs and stats
```
docker container logs -f web1
docker container stats
docker container inspect <CONTAINER-NAME-OR-ID>
```

## Logging into a container and inspecting the FS
```
docker container exec -it web1 sh
```
## Running custom scripts

You create a script (e.g. *docker-entrypoint.sh*) and then you add into your *Dockerfile* something like the following. 

Note: we put stuff into the root directory because we want to keep the entrypoint scripts separated from the app code.
```
COPY docker-entrypoint.sh /
RUN chmod +x /docker-entrypoint.sh
ENTRYPOINT ["/docker-entrypoint.sh"]

CMD ["flask", "run", "--host=0.0.0.0", "--port=5000"]
```
By default, the **ENTRYPOINT** instruction will use */bin/sh -c* for running whatever is specified in the **CMD** instruction.
So in our script, we are basically overriding the start command with some script that must do something and still run the CMD-stuff!

The script will be run everytime the Docker image is run!

A sample *docker-entrypoint.sh*:
```
#!/bin/sh
set -e

echo "The Dockerfile ENTRYPOINT has been executed!"

export WEB2_COUNTER_MSG="${WEB2_COUNTER_MSG:-carbon based life forms have sensed this website}"

exec "$@"
```

With this script, we can pass different environment variables:
```
docker container run -it --rm --name webscripted -p 5001:5000 -e FLASK_APP=app.py -e FLASK_DEBUG=1 -e WEB2_COUNTER_MSG="Docker fans have visited this page" --net firstnetwork -v $(readlink -f $PWD):/app webscripted:latest
```

In this way we can set defaults in the script and override them with command line (e.g., perform database migrations).

## Docker clean up and troubleshooting

How much space are we consuming with our images?
```
docker system df
TYPE                TOTAL               ACTIVE              SIZE                RECLAIMABLE
Images              18                  0                   618.7MB             618.7MB (100%)
Containers          0                   0                   0B                  0B
Local Volumes       1                   0                   98B                 98B (100%)
Build Cache         0                   0                   0B                  0B
```

This also dumps volumes and other things:
```
docker system df -v
```

### Cleaning up dangling stuff and more

Every image with *<none>* is safe to delete
```
docker image ls

REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
webscripted         latest              6e3fc5d5cf52        32 minutes ago      110MB
weboptimized        latest              e6163879bd04        48 minutes ago      103MB
web3                latest              145f2ff1de1d        About an hour ago   110MB
<none>              <none>              a069e54648e9        10 hours ago        110MB
web2                latest              52e0ea46548b        11 hours ago        110MB
web1                latest              ec34627d584b        14 hours ago        73.1MB
<none>              <none>              69688ef2c78a        14 hours ago        73.1MB
...
```

We can remove unused resources:
```
docker system prune -f
```

Or remove everything we have locally:
```
docker system prune -f -a
```

## Report your system configuration

```
docker system info
```

## Stopping all running containers at once

```
docker container stop $(docker container ls -aq)
```
