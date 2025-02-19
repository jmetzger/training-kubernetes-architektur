# Exercise: Nginx Content 

## Prerequisites 

  * nfs-csi is installed
  * storage-class nfs-csi is setup

```
nano 01-sc.yaml
```

```
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: nfs-csi
provisioner: nfs.csi.k8s.io
parameters:
  server: 10.135.0.13
  share: /var/nfs
reclaimPolicy: Delete
volumeBindingMode: Immediate
mountOptions:
  - nfsvers=3
```

## Frage: Wo liegen in nginx die Daten 

```
kubectl run nginx --image nginx:1.23 
kubectl get pods 
kubectl exec -it nginx -- bash
# im pod 
cd /usr/share/nginx/html
echo "hallo jochen war hier" > index.html
echo "hallo jochen war hier" > testseite.html

#  10.108.0.169
kubectl run -it podtester --image=busybox
# in der busybox 
wget -O - http://10.108.0.169
wget -O - http://10.108.0.169/testseite.html
```

## Schritt 1: PVC anlegen 

```
nano 01-pvc-nfs-nginx.yaml
```

```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-nfs-nginx 
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 2Gi
  storageClassName: nfs-csi
```

```
kubectl apply -f .
# Steht das Ding auf Bound ? 
kubectl get pvc 
```

## Schritt 2: Deployment + Service 

```
nano 02-deploy.yml 
```

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-nginx
spec:
  selector:
    matchLabels:
      app: my-nginx
  replicas: 3
  template:
    metadata:
      labels:
        app: my-nginx
    spec:
      containers:
      - name: my-nginx
        image: nginx:1.23
        ports:
        - containerPort: 80
        volumeMounts:
        - name: persistent-storage
          mountPath: "/usr/share/nginx/html"
          readOnly: false
      volumes:
      - name: persistent-storage
        persistentVolumeClaim:
          claimName: pvc-nfs-nginx
```

```
nano 03-svc.yaml 
```


```
apiVersion: v1
kind: Service
metadata:
  name: nginx
spec:
  type: LoadBalancer
  ports:
  - port: 80
    protocol: TCP
  selector:
    app: my-nginx
```

```
kubectl apply -f .
# Get External IP 
kubectl get svc
kubectl get pods
curl http://146.190.178.80
```

## Schritt 3: Content reinschreiben 

```
kubectl exec -it deployment/my-nginx -- bash
```

```
echo "Hans ist nicht Jochen" > /usr/share/nginx/html/index.html
exit 
```

```
# Überprüfen, ob das funktioniert
curl http://146.190.178.80 
```

```
kubectl delete deploy my-nginx
```

```
kubectl apply -f .
```

```
# Überprüfen, ob das immer noch funktioniert
curl http://146.190.178.80 
```
