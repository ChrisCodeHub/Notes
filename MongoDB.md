# Notes on how to spin up a simple docker container containing MongoDB

spin up a docker container with mongoDB inside, 
map database to a volume to preserve data
export ports so can be accessed from host (its using the host network)

```bash
docker volume create mongodemo
docker run -d \
--name mongo-demo \
-e MONGO_INITDB_ROOT_USERNAME=mongoadmin \
-e MONGO_INITDB_ROOT_PASSWORD=LikeAndSubscribe \
-p 27017:27017 \
-v mongodemo:/data/db \
mongo:7.0```

# shell into container
```bash
docker exec -it mongo-demo bash
```

# once inside container, access mongo command line
```
mongosh localhost:27000
```


# adding to a docker compose file

```bash
version: '3.8'
services:
  mongo: 
    image: mongo:7.0
    environment:
      MONGO_INITDB_ROOT_USERNAME: mongadmin
      MONGO_INITDB_ROOT_PASSWORD: LikeAndSubscribe
    ports:
      - 27017:27017
    volumes:
      - mongodata:/data/db
volumes:
  mongodata:
    driver: local
```


