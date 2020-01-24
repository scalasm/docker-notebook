# Networking

## Show networks
```
docker network ls

NETWORK ID          NAME                DRIVER              SCOPE
244af1290ebe        bridge              bridge              local
7b5f66a8317d        host                host                local
f2831a734437        none                null                local
```

The *bridge* network is the default network: if we don't specify anything else then Docker will use it!

```
docker network inspect bridge
```

## Create a network
```
docker network create --driver bridge firstnetwork
```
Note: the *bridge* drivers if only for single host: if you want to connect containers spawning several hosts, then use *overlay*.

## Starting other containers

```
docker pull redis:3.2-alpine
docker container run --rm -itd -p 6379:6379 --name redis --net firstnetwork redis:3.2-alpine

docker container run -it --rm --name web2 -p 5001:5000 -e FLASK_APP=app.py -e FLASK_DEBUG=1 -d --net firstnetwork -v $(readlink -f $PWD):/app web2:latest
```

## Poking with Redis
```
PS C:\Users\mario\Dropbox\acloudguru\docker-fundamentals> docker exec -it redis redis-cli
127.0.0.1:6379> KEYS *
1) "web2_counter"
127.0.0.1:6379> INCRBY web2_counter 100000
(integer) 100005
```

## Named volumes
```
docker volume create web2_redis
```

```
docker volume ls
DRIVER              VOLUME NAME
local               b2d7a0585411454e7f6d85a5b9d654e2afe9759ff39fc06de4812e0ea9ebf67e
local               web2_redis
```

Now we can start the redis container so that it will run will persist stuff across restarts
```
docker container run --rm -itd -p 6379:6379 --name redis --net firstnetwork -v web2_redis:/data redis:3.2-alpine
```

Note: look at the README for every image in order to make it persistent across restarts: everybody has 
his own idea about the directory name!

To force Redis to persist data on disk:
```
docker exec -it redis redis-cli
```

## Executing commands and go away 

Dump the /etc/hosts files
```
docker exec redis cat /etc/hosts
```
