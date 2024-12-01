This document is a natural continution of the simpleAppInK8s.md doc
Idea of this is to evolve a deployment that has postgres deployed into a cluter
based off the digital ocean article and helped by chatGPT ¯\_(ツ)_/¯  

The initial parts of this are definetly not product ready, more useful for throwing something
together to investigate how postgres can sit in a cluster, later version my evolve the secret handling and
load balancing to be more product ready

Starting with a deployment for now, should move to a stateful set in reality
secret in config map... really you should not be that silly in production, but again this is just quick sandpit
use secrets for that....

postgres comes with docker images already available.  Since I will be using a KIND cluster I need to download that image
```
docker pull postgres:latest
docker images
```
once present it will need loading into teh kind cluster so that its available for a deployment
start the cluster and load the image into it

```bash
kind create cluster
kind load docker-image postgres:latest
docker exec -it my-node-name crictl images    # to see what is loaded
kubectl create namespace pizza-app
```

chatGPT wrote a simple deployment yaml for the postgres file
```yaml
# deployment.yaml for postgres that uses an ephemeral disk for storage
apiVersion: apps/v1
kind: Deployment
metadata:
  name: postgres
  labels:
    app: postgres
spec:
  replicas: 1
  selector:
    matchLabels:
      app: postgres
  template:
    metadata:
      labels:
        app: postgres
    spec:
      containers:
      - name: postgres
        image: postgres:latest # Adjust version as needed
        ports:
        - containerPort: 5432
        env:
        - name: POSTGRES_USER           # do not do this in production, only for sandboxes where you can 
          value: "testuser"             # store password into git and not care
        - name: POSTGRES_PASSWORD
          value: "testpassword"
        - name: POSTGRES_DB
          value: "testdb"
        volumeMounts:
        - mountPath: /var/lib/postgresql/data
          name: postgres-data
      volumes:
      - name: postgres-data
        emptyDir: {}            # this create a temp, ephemeral disk that postgres will use
---
apiVersion: v1
kind: Service
metadata:
  name: postgres-service
spec:
  selector:
    app: postgres
  ports:
    - protocol: TCP
      port: 5432
      targetPort: 5432
  type: ClusterIP                   # clusterIP since teh (eventual) connection will be from internal pods only
```

```
kubectl -n pizza-app apply -f pizza-deployment.yaml
```

to test
connect to postgres


Create a new database:

```
CREATE DATABASE mydatabase;
```
 

Connect to mydatabase:
```
\c mydatabase
```

Create a table, insert data:
```
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    name VARCHAR(128),
    email VARCHAR(128)
);

INSERT INTO users (name, email) VALUES ('Attila', 'attila@example.com'), ('Ghengis', 'ghengis@example.com');
``` 

Run a sample query:

```
SELECT * FROM users;
```

Output:
 id | name    |  	email  	 
----+---------+------------------
  1 | Attila  | attila@example.com
  2 | Ghengis | ghengis@example.com