# 
```
docker image build -t web3 .
docker container run -it --rm --name web3 -p 5001:5000 -e FLASK_APP=app.py -e FLASK_DEBUG=1 -d --net firstnetwork -v $(readlink -f $PWD):/app web3:latest
```

For Redis we re-used the volume from the previous container (web2) with *--volumes-from*:
```
docker container run --rm -itd -p 6379:6379 --name redis --net firstnetwork -v web2_redis:/data --volumes-from web3 redis:3.2-alpine
```