# Helm - Grundlagen

## Wo ? 

```
Telefonbuch für die Helm-Charts 
```

 * https://artifacthub.io/

## Komponenten 

```
Chart - beeinhaltet Beschreibung und Komponenten 
1) url 
2) tar.gz / tgz - Format 
3) oder Verzeichnis 

Wenn wir ein Chart ausführen wird eine Release erstellen 
(parallel: image -> container, analog: chart -> release)
```

## Installation 

```
# Beispiel ubuntu 
# snap install --classic helm

# Cluster muss vorhanden, aber nicht notwendig wo helm installiert 

# Voraussetzung auf dem Client-Rechner (helm ist nichts als anderes als ein Client-Programm) 
Ein lauffähiges kubectl auf dem lokalen System (welches sich mit dem Cluster verbinden kann).
-> saubere -> .kube/config 

# Test
kubectl cluster-info 

```

