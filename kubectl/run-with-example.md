# Einen Pod starten (und wieder löschen)

## Beispiel 1 (das funktioniert)

```
# Zeigt mir die Pods die laufen
kubectl get pods 

# Aufbau des Befehls  
# kubectl run NAME --image=IMAGE_EG_FROM_DOCKER

# Ein Beispiel
kubectl run nginx --image=nginx:1.25.1

kubectl get pods 
# Alle nodes anzeigen
kubectl get nodes -o wide 
# Auf welchem Node läuft der Pods
kubectl get pods -o wide 
```

## Beispiel 2 (das nicht funktioniert !!)

```
kubectl run meinfoo --image=foo2
# ImageErrPull - Image konnte nicht geladen werden 
kubectl get pods 
# Weitere status - info 
kubectl describe pods meinfoo
```

## Beide Pods wieder löschen

```
kubectl delete pods nginx meinfoo
kubectl get pods
```

## Referenz:

  * https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#run
