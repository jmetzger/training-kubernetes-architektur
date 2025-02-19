# Install mariadb with helm chart 

## Walkthrough 

  * https://artifacthub.io/packages/helm/bitnami/mariadb?modal=install

```
helm repo add bitnami https://charts.bitnami.com/bitnami
# helm install -n <yournewnamespace> --create-namespace my-mariadb bitnami/mariadb --version 20.2.4
# e.g. 
helm install -n jochen1 --create-namespace my-mariadb bitnami/mariadb --version 20.2.4

# helm list -n <yournewnamespace>
helm list -n jochen1
```
