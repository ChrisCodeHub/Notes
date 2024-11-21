This document covers the basics of deploying a simple python app into k8's
The app is a simple hellow world flask app that just responds to any access on "/"  with hello world

The point of this code is that it 

puts a simple web server into a Dockerfile
then puts that docker file into a deployment file
also defines a service so that the end point inside the cluster is available
finally creates a helm chart to assist in that deployment

so its simple - and is just meant to show the basics of deploying a web-server into k8s
It has no authentications etc... thats in other repos.
Start small, build up to a full featured beastie

the dockerfile 
```Dockerfile
FROM python:3.7

RUN mkdir /app
WORKDIR /app
ADD . /app/
RUN pip install -r requirements.txt

EXPOSE 5000
CMD ["python", "/app/main.py"]
```

requirements.txt is just flash
```
Flask
```

main.py is as simple a flask app as can be
```
from flask import Flask
app = Flask(__name__)

@app.route("/")
def hello():
    return "Hello from Python!"

if __name__ == "__main__":
    app.run(host='0.0.0.0')
```

deployment.yaml - note its two files in one using the `---` to join
```yaml
apiVersion: v1
kind: Service
metadata:
  name: hello-python-service
spec:
  selector:
    app: hello-python
  ports:
  - protocol: "TCP"
    port: 6000
    targetPort: 5000
  type: LoadBalancer


---
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: hello-python
spec:
  replicas: 4
  template:
    metadata:
      labels:
        app: hello-python
    spec:
      containers:
      - name: hello-python
        image: hello-python:latest
        imagePullPolicy: Never
        ports:
        - containerPort: 5000
```
