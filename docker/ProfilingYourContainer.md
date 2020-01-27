# Profiling your container

# Get your container stats

## Requirements

* Your container is up and running

## Commands
```
docker stats <CONTAINER-NAME-OR-ID>
```

For example:
```
docker stats sparkasse-docker-compose-stack_core-banking-service_1
```
will show something like (continuosly updating)
```
CONTAINER ID        NAME                                                    CPU %               MEM USAGE / LIMIT     MEM %               NET I/O             BLOCK I/O           PIDS
5b268c864264        sparkasse-docker-compose-stack_core-banking-service_1   0.13%               174.2MiB / 1.943GiB   8.75%               2.88kB / 1.08kB     24MB / 0B           35
```

# Measuring performance under load 

# Pre-requisites
Ensure you have it:

```
docker pull williamyeh/wrk
```

## Run load tests

```
docker run --rm williamyeh/wrk -t<# of CLIENT THREADS> -c<# of HTTP OPEN CONNECTIONS> -d<TEST DURATION> http://<MACHINE_IP_AND_PATH>
```

For example:
```
export MACHINE_IP=$(hostname -I | cut -d ' ' -f 1)
docker run --rm williamyeh/wrk -t10 -c5000 -d10s http://$MACHINE_IP/api/movimenti
```

**NOTE** You may also want to keep the *docker stats* open in a terminal to watch CPU / Memory / I/O are behaving. 

## Sample load results

Running on my local VM to check if the reverse proxy is adding latency or not:


* Case #1 - REST API Calls w/ NGINX as reverse proxy
```
PS C:\Users\SCALASM> docker run --rm williamyeh/wrk -t10 -c50 -d10s http://10.0.99.24/api/movimenti
Running 10s test @ http://10.0.99.24/api/movimenti
  10 threads and 50 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency    40.19ms   17.03ms 174.57ms   77.28%
    Req/Sec   126.22     20.89   200.00     68.50%
  12589 requests in 10.02s, 6.47MB read
Requests/sec:   1255.93
Transfer/sec:    661.03KB
```

* Case #1 - REST API Calls w/o NGINX as reverse proxy
```
PS C:\Users\SCALASM> docker run --rm williamyeh/wrk -t10 -c50 -d10s http://10.0.99.24:8002/movimenti
Running 10s test @ http://10.0.99.24:8002/movimenti
  10 threads and 50 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency    26.26ms   19.65ms 227.09ms   81.81%
    Req/Sec   207.26     44.03   370.00     68.10%
  20677 requests in 10.03s, 9.73MB read
Requests/sec:   2061.43
Transfer/sec:      0.97MB
```

As you can see, the performance are quite different:
* the NGINX container seems to be introducing a lot of latency: this may due to incorrect configuration since NGINX is known for its high performances!
* this may also be an issue by the testing client

If you increase the number of concurrent open HTTP requests, you can get different numbers:

* Case #1 (w/ NGINX)
```
PS C:\Users\SCALASM> docker run --rm williamyeh/wrk -t10 -c5000 -d10s http://10.0.99.24/api/movimenti
Running 10s test @ http://10.0.99.24/api/movimenti
  10 threads and 5000 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency     1.72s   114.72ms   2.00s    84.39%
    Req/Sec    62.85     83.43   484.00     87.06%
  2384 requests in 10.11s, 1.23MB read
  Socket errors: connect 0, read 7597, write 0, timeout 1801
Requests/sec:    235.81
Transfer/sec:    124.12KB
```

* Case 2 (w/o NGINX)
```
PS C:\Users\SCALASM> docker run --rm williamyeh/wrk -t10 -c5000 -d10s http://10.0.99.24:8002/movimenti
Running 10s test @ http://10.0.99.24:8002/movimenti
  10 threads and 5000 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency   577.56ms  676.15ms   2.00s    78.28%
    Req/Sec   232.99    128.12   780.00     71.34%
  18638 requests in 10.10s, 8.77MB read
  Socket errors: connect 0, read 0, write 0, timeout 8501
Requests/sec:   1844.93
Transfer/sec:      0.87MB
```

So, in this way we are getting really poor performances in both cases ...
