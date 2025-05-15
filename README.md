# MongoDB Replica Set Deployment on Kubernetes

This project demonstrates how to deploy a MongoDB Replica Set using Kubernetes StatefulSets as part of the **SIT-737 Cloud Native Application Development** assignment.

---

## 📁 Folder Structure

```
project-root/
├── createStorageClass.yaml
├── createConfigMap.yaml
├── createMongoDbSecret.yaml
├── createStatefulSet.yaml
├── createHeadlessService.yaml
```

---

## 🧾 Step-by-Step Commands

### 1. ✅ Check Kubernetes Cluster
```bash
kubectl get pods
```

### 2. ✅ Apply Kubernetes Configuration Files
```bash
kubectl apply -f .\createStorageClass.yaml
kubectl apply -f .\createConfigMap.yaml
kubectl apply -f .\createMongoDbSecret.yaml
kubectl apply -f .\createStatefulSet.yaml
kubectl apply -f .\createHeadlessService.yaml
```

### 3. ✅ Access the Primary MongoDB Pod
```bash
kubectl exec -it mongo-0 -- /bin/bash
mongo
```

### 4. ✅ Initialize the Replica Set
```js
rs.initiate({
  _id: "rs0",
  members: [
    { _id: 0, host: "mongo-0.mongo.default.svc.cluster.local:27017" },
    { _id: 1, host: "mongo-1.mongo.default.svc.cluster.local:27017" },
    { _id: 2, host: "mongo-2.mongo.default.svc.cluster.local:27017" }
  ]
})
```

### 5. ✅ Create Database and Collection
```js
use test
db.createCollection("cardetaile")
```

### 6. ✅ Perform CRUD Operations
```js
db.cardetaile.insert({ name: "Tesla" })

db.cardetaile.updateOne(
  { name: "Tesla" },
  { $set: { name: "Tesla Model X" } }
)

db.cardetaile.insert({ name: "BMW" })

db.cardetaile.deleteOne({ name: "Tesla" })
db.cardetaile.deleteOne({ name: "Tesla Model X" })
```

### 7. ✅ Verify Replication on Secondary Pods

#### mongo-1:
```bash
kubectl exec -it mongo-1 -- /bin/bash
mongo
rs.slaveOk()
use test
db.cardetaile.find()
```

#### mongo-2:
```bash
kubectl exec -it mongo-2 -- /bin/bash
mongo
rs.slaveOk()
use test
db.cardetaile.find()
```

---

## ✅ Output Verification

- MongoDB replica set initialized successfully.
- Performed insert, update, and delete operations.
- Verified that data was replicated across all 3 MongoDB pods.

---

## 📌 Notes

- Ensure Minikube or Docker Desktop is running.
- Use `kubectl get pods` to monitor the health of all pods.
- All MongoDB shell commands are executed inside pods (`mongo-0`, `mongo-1`, `mongo-2`).

---

Created for **SIT-737 Assignment – Cloud Native Application Development**.
