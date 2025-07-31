## MongoExpress and MongoDB Project

![alt text](image.png)


![alt text](image-1.png)

### Step1
Keep the CLuster Ready  

```
kubectl get all 
```

1.Create a secret file mongodb-secret.yml    
2.Create mongo.yml --> For deployment (Backend)  
3.Create a Service for mongodb in mongo.yml  
4.Mongo Express Deployment & Service & ConfigMap --> mongo-express.yaml (frontend)  



Secret Configuration file mongodb-secret.yml
----------------------------
```
vi mongodb-secret.yml
```

```
apiVersion: v1
kind: Secret
metadata:
    name: mongodb-secret
type: Opaque
data:
    mongo-root-username: dXNlcm5hbWU=
    mongo-root-password: cGFzc3dvcmQ=
```

In terminal , 
```
echo -n 'username' | base64
```
```
echo -n 'password' | base64
```

 
```
kubectl apply -f mongo-secret.yml
```
```
kubectl get secrets
```



Create a MongoDB Deployment
-------------------------

Note: The below yaml says, to create a single pod(replica 1) and a container name mongodb with image mongo (gets from dockerhub)  
      --> MongoDB container database port mention in dockerhub is 27017 default  
--> go to dockerhub and search for mongo and see the Environment Variables, it contains variables (MONGO_INITDB_ROOT_USERNAME and MONGO_INITDB_ROOT_PASSWORD)  

Start writing from  after image  

        ports:  
        - containerPort: 27017  

lets write env variables because we need to pass username and password but cannot pass here directly, for that we need to create a separate yaml mongodb-secret.yml (Secret Configuration file)  



Now we can reference what ever we have mentioned in mongo-secret.yml in mongo.yml for username and passwords  
```
    valueFrom:
                secretKeyRef:
                name: mongodb-secret
                key: mongo-root-username
```
---> after mongo.yml create , lets create a deployment  

```
vi mongo.yml
```

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mongodb-deployment
  labels:
    app: mongodb
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mongodb
  template:
    metadata:
      labels:
        app: mongodb
    spec:
      containers:
      - name: mongodb
        image: mongo
        ports:
        - containerPort: 27017
        env:
        - name: MONGO_INITDB_ROOT_USERNAME
          valueFrom:
            secretKeyRef:
              name: mongodb-secret
              key: mongo-root-username
        - name: MONGO_INITDB_ROOT_PASSWORD
          valueFrom: 
            secretKeyRef:
              name: mongodb-secret
              key: mongo-root-password
```


```
kubectl apply -f mongo.yml
```
```
kubectl get all
```
```
kubectl get pods
```
```
kubectl get pod --watch
```
```
kubectl describe pod podname
```
[mention podname, and to see any issues with pod]


---> lets now create a service  
In yaml we can put multiple documents using ---  

```
vi mongo.yml and add service
```

```
---
apiVersion: v1
kind: Service
metadata:
  name: mongodb-service
spec:
  selector:
    app: mongodb
  ports:
    - protocol: TCP
      port: 27017
      targetPort: 27017
```

Note:   
----
Kind = Service  
metadata/name = a random name  
selector = to connect to pod through app name mongodb  
port = service port   
targetport = container port of deployment  

```
kubectl apply -f mongo.yml
```

```
kubectl get service
```

```
kubectl describe service mongodb-service
```

```
kubectl get pod -o wide
```



Mongo Express Deployment & Service & ConfigMap
--------------------------------

Create a configmap for DB URL  
database url = database server is same as service name in mongo.yml mongodb-service  

```
vi mongo-configmap.yaml
```


```
apiVersion: v1
kind: ConfigMap
metadata:
  name: mongodb-configmap
data:
  database_url: mongodb-service
```

```
kubectl apply -f mongo-configmap.yaml
```

```
kubectl get pods
```


Now create a mongo-service (frontend)  

```
vi mongo-express.yaml
```

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mongo-express
  labels:
    app: mongo-express
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mongo-express
  template:
    metadata:
      labels:
        app: mongo-express
    spec:
      containers:
      - name: mongo-express
        image: mongo-express
        ports:
        - containerPort: 8081
        env:
        - name: ME_CONFIG_MONGODB_ADMINUSERNAME
          valueFrom:
            secretKeyRef:
              name: mongodb-secret
              key: mongo-root-username
        - name: ME_CONFIG_MONGODB_ADMINPASSWORD
          valueFrom: 
            secretKeyRef:
              name: mongodb-secret
              key: mongo-root-password
        - name: ME_CONFIG_MONGODB_SERVER
          valueFrom: 
            configMapKeyRef:
              name: mongodb-configmap
              key: database_url
```

```
kubectl apply -f mongo-express.yaml
```

```
kubectl get pods
```
```
kubectl logs podname
```

Now lets create a Service for mongoexpress  `
```
vi mongo-express.yaml 
```
add service in same file

```
---
apiVersion: v1
kind: Service
metadata:
  name: mongo-express-service
spec:
  selector:
    app: mongo-express
  type: LoadBalancer  
  ports:
    - protocol: TCP
      port: 8081
      targetPort: 8081
      nodePort: 30000
```

```
kubectl apply -f mongo-express.yaml
```

```
kubectl get service
```
```
http://loadbalancer:8081
```
username: admin  
password: pass  

Note: if you don't want to expose with 8081 change port : 8081 to port : 80  


------------------------
```
vi mongo.yaml
```


```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mongodb-deployment
  labels:
    app: mongodb
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mongodb
  template:
    metadata:
      labels:
        app: mongodb
    spec:
      containers:
      - name: mongodb
        image: mongo
        ports:
        - containerPort: 27017
        env:
        - name: MONGO_INITDB_ROOT_USERNAME
          valueFrom:
            secretKeyRef:
              name: mongodb-secret
              key: mongo-root-username
        - name: MONGO_INITDB_ROOT_PASSWORD
          valueFrom: 
            secretKeyRef:
              name: mongodb-secret
              key: mongo-root-password
---
apiVersion: v1
kind: Service
metadata:
  name: mongodb-service
spec:
  selector:
    app: mongodb
  ports:
    - protocol: TCP
      port: 27017
      targetPort: 27017
```