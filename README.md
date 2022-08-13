
## Replication

Documentation [here](https://redis.io/topics/replication)

### Configuration
```
#persistence
dir /data
dbfilename dump.rdb
appendonly yes
appendfilename "appendonly.aof"
```
### redis-0 Configuration
```
protected-mode no
port 6379

#authentication
masterauth 1jdk.admin
requirepass 1jdk.admin
```

### redis-1 Configuration
```
protected-mode no
port 6379
slaveof redis-0 6379

#authentication
masterauth 1jdk.admin
requirepass 1jdk.admin
```

### redis-2 Configuration
```
protected-mode no
port 6379
slaveof redis-0 6379

#authentication
masterauth 1jdk.admin
requirepass 1jdk.admin
```

## Running Sentinels

Documentation [here](https://redis.io/topics/sentinel)

## Basic Config
```
port 5000
sentinel monitor redis-m redis-0 6379 2
sentinel down-after-milliseconds redis-m 5000
sentinel failover-timeout redis-m 60000
sentinel parallel-syncs redis-m 1
sentinel auth-pass redis-m 1jdk.admin

```
Starting Redis in sentinel mode

```
docker network create redis


docker run -d --rm --name redis-0 `
--net redis `
-v ${PWD}/redis-0:/etc/redis/ `
redis:6.0-alpine redis-server /etc/redis/redis.conf

docker run -d --rm --name redis-1 `
--net redis `
-v ${PWD}/redis-1:/etc/redis/ `
redis:6.0-alpine redis-server /etc/redis/redis.conf

docker run -d --rm --name redis-2 `
--net redis `
-v ${PWD}/redis-2:/etc/redis/ `
redis:6.0-alpine redis-server /etc/redis/redis.conf

docker run -d --rm --name sentinel-0 --net redis `
    -v ${PWD}/sentinel-0:/etc/redis/ `
    redis:6.0-alpine `
    redis-sentinel /etc/redis/sentinel.conf

docker run -d --rm --name sentinel-1 --net redis `
    -v ${PWD}/sentinel-1:/etc/redis/ `
    redis:6.0-alpine `
    redis-sentinel /etc/redis/sentinel.conf

docker run -d --rm --name sentinel-2 --net redis `
    -v ${PWD}/sentinel-2:/etc/redis/ `
    redis:6.0-alpine `
    redis-sentinel /etc/redis/sentinel.conf


docker logs sentinel-0
docker exec -it sentinel-0 sh
redis-cli -p 5000
info
sentinel master redis-m

# clean up 

docker rm -f redis-0 redis-1 redis-2
docker rm -f sentinel-0 sentinel-1 sentinel-2


```
