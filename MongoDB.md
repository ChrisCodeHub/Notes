Notes on how to spin up a simple docker container containing MongoDB

spin up a docker container with mongoDB inside, 
map database to a volume to preserve data
export ports so can be accessed from host (its using the host network)

```bash
docker volume create mongodata
docker run -d -v mongodata:/data/db --name mymongo --net=host mongo:latest --bind_ip 127.0.0.1 --port 27000
```

shell into container
```bash
docker exec -it mymongo bas
```

once inside container, access mongo command line
```
mongosh localhost:27000
```


