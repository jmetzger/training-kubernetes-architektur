# Example of triggering oom killer and savely fire up new pods in new replicaset 

## What to achieve ?

 1. Deploy a working version
 2. Deploy a new version that fails with OOM-Killer (but we can be sure pod from old replicaset still works) 

## Hint: more than 1 replica 

  * If you have more than one replicasm, system will still work
  * Keeping replicas - 1 (So 3 replicas, still 2 left)  

## Step 1: Create deployment that works 

```
cd
mkdir -p manifests
cd manifests
mkdir -p stress
cd stress
```

```
nano deployment.yml
```

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: deploy-memtest
spec:
  minReadySeconds: 120
  selector:
    matchLabels:
      app: memtest
  replicas: 1
  template:
    metadata:
      labels:
        app: memtest
    spec:
      containers:
      - name: memory-demo-ctr
        image: polinux/stress
        resources:
          requests:
            memory: "100Mi"
          limits:
            memory: "200Mi"
        command: ["stress"]
        args: ["--vm", "1", "--vm-bytes", "150M", "--vm-hang", "1"]

```

```
kubectl apply -f .
kubectl get all
kubectl get pods 
```

## Schritt 2: Now with oom - killer version 

  * More memory than available
  * So new pods fail (normally, old pods would be terminated
  * But: Due to minReadySeconds (each pod must at least 120seconds before state is switched to ready)
    * System will wait / and old pods are still availale

```
Change line --args from: --vm-bytes 150M to --vm-bytes 250M
```
   
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: deploy-memtest
spec:
  minReadySeconds: 120
  selector:
    matchLabels:
      app: memtest
  replicas: 1
  template:
    metadata:
      labels:
        app: memtest
    spec:
      containers:
      - name: memory-demo-ctr
        image: polinux/stress
        resources:
          requests:
            memory: "100Mi"
          limits:
            memory: "200Mi"
        command: ["stress"]
        args: ["--vm", "1", "--vm-bytes", "250M", "--vm-hang", "1"]

```

```
kubectl get all
kubectl apply -f .
kubectl get all
# after a while we will see the new pod being in mode OOMKiller 
watch -n 1 -d kubectl get pods 
```
