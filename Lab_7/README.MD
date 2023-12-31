# Lab 7

## Auteurs

Alexandre Chauchard
Raphaël Hilt

## Partie 1 - Installer Minikube

On utilise la commande suivante (Mac puce Intel) :

``` bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-darwin-amd64
sudo install minikube-darwin-amd64 /usr/local/bin/minikube
```

Puis après avoir ouvert l'application Docker :

``` bash
minikube start --driver=docker
```

Et finalement : 

``` bash
minikube status 
```
Pour vérifier que tout est ok

Dans notre cas c'est bon.

## Partie 2 - Apprendre à utiliser les commandes `kubectl`

### 2.2 Créer un pod

On créer un déploiement avec un `pod` avec la commande suivante :

``` bash 
kubectl create deployment kubernetes-bootcamp --image=gcr.io/google-samples/kubernetes-bootcamp:v1
```

### 2.3 Lister les pods

On liste les pods qui s'effectuent :

``` bash
kubectl get pods
```

On constate uniquement celui qu'on vient d'ouvir.

### 2.4 Obtenir les logs

On montre les logs avec la commande suivante :

``` bash
kubectl logs kubernetes-bootcamp-855d5cc575-hvwpw
```

Voici le résultat :

``` bash
Kubernetes Bootcamp App Started At: 2023-10-24T07:21:14.113Z | Running On:  kubernetes-bootcamp-855d5cc575-hvwpw 
```

### 2.5 Lancer une commande

On lance ensuite une commande avec cette ligne là :

``` bash
kubectl exec kubernetes-bootcamp-855d5cc575-hvwpw -- cat /etc/os-release
```

voici le résultat :

``` bash
PRETTY_NAME="Debian GNU/Linux 8 (jessie)"
NAME="Debian GNU/Linux"
VERSION_ID="8"
VERSION="8 (jessie)"
ID=debian
HOME_URL="http://www.debian.org/"
SUPPORT_URL="http://www.debian.org/support"
BUG_REPORT_URL="https://bugs.debian.org/"
```

### 2.6 Ouvrir une Shell dans le pod

On utilise cette ligne :

``` bash
kubectl exec -ti kubernetes-bootcamp-855d5cc575-hvwpw bash
```

### 2.7 Lister les fichiers

On utilise :

``` bash
ls
```

On repère le fichier javaScript `server.js`

### 2.8 Faire une requête pour valider le fonctionement

Avec la ligne suivante on peut trouver le port :

``` bash
cat server.js
```

On fait la requête comme suit :

``` bash
curl http://localhost:8080
```

Et voici le résultat :

``` bash
Hello Kubernetes bootcamp! | Running on: kubernetes-bootcamp-855d5cc575-hvwpw | v=1
```

### 2.9 Requête hors du pod 

Avec la même commande de requête, on n'obtient pas de résultat car on ne peut pas atteindre le port.

## Partie 3 - Apprendre à exposer Kubernetes à l'extérieur

### 3.1 Exposer le déploiement

Nous utilisons la commande suivante :

``` bash
kubectl expose deployments/kubernetes-bootcamp --type="NodePort" --port 8080
```

### 3.2 Trouver le port attaché au service

En utilisant cette commande :

``` bash
kubectl get services
```

Qui renvoie ceci :

``` bash
kubernetes-bootcamp   NodePort    10.104.144.190   <none>        8080:30963/TCP   27s
```

### Partie 3.3 Obtenir l'ip

Avec la commande suivante :

``` bash
minikube ip
```

Et on obtient ; `192.168.49.2`.

### Partie 3.4 Accéder à l'app sur internet

Vu qu'on utilise Docker on créer un tunnel vers le noeux avec cette commande :

``` bash
minikube service kubernetes-bootcamp
```

On voit alors `Hello Kubernetes Bootcamp` sur notre page web


## Partie 4 - Apprendre à changer l'échelle d'un déploiement Kubernetes

### 4.1 Augmenter la taille à 5 pods

On utilise la commande suivante :

``` bash
kubectl scale deployments/kubernetes-bootcamp --replicas=5
```

### 4.2 Vérifier que l'on a bien 5 pods

On utilise cette commande :

``` bash
kubectl get pods
```

Elle renvoie cette fois :

``` bash
NAME                                   READY   STATUS    RESTARTS   AGE
kubernetes-bootcamp-855d5cc575-9znjz   1/1     Running   0          41s
kubernetes-bootcamp-855d5cc575-hvwpw   1/1     Running   0          54m
kubernetes-bootcamp-855d5cc575-l4sds   1/1     Running   0          41s
kubernetes-bootcamp-855d5cc575-rw9wk   1/1     Running   0          41s
kubernetes-bootcamp-855d5cc575-v2bqs   1/1     Running   0          41s
```

### 4.3 Vérifier la page Web

On voit que le nom du pod change.

### 4.4 Réduire à 2 pods

On utilise la commande qui suit :

``` bash
kubectl scale deployments/kubernetes-bootcamp --replicas=2
```

Puis `kubectl get pods` et on voit que :

```bash 
NAME                                   READY   STATUS        RESTARTS   AGE
kubernetes-bootcamp-855d5cc575-9znjz   1/1     Running       0          3m7s
kubernetes-bootcamp-855d5cc575-hvwpw   1/1     Running       0          56m
kubernetes-bootcamp-855d5cc575-l4sds   1/1     Terminating   0          3m7s
kubernetes-bootcamp-855d5cc575-rw9wk   1/1     Terminating   0          3m7s
kubernetes-bootcamp-855d5cc575-v2bqs   1/1     Terminating   0          3m7s
```

## Partie 5 - Lancer une application à plusieurs Pods

### 5.1 Mettre à jour l'image docker

Avec la commande suivante :

``` bash
kubectl set image deployments/kubernetes-bootcamp kubernetes-bootcamp=jocatalin/kubernetes-bootcamp:v2
```

Il faut actualiser la page plusieurs fois pour voir.

### 5.2 Résultats 

?

### 5.3 Modifier à nouveau

Avec cette commande :

```bash
kubectl set image deployments/kubernetes-bootcamp kubernetes-bootcamp=jocatalin/kubernetes-bootcamp:v3
```

### 5.4 Lister les pods

Avec cette commande :

``` bash
kubectl get pods
```

On obtient :

``` bash 
NAME                                   READY   STATUS         RESTARTS   AGE
kubernetes-bootcamp-66b8fc57cb-2l8wm   0/1     ErrImagePull   0          33s
kubernetes-bootcamp-69b6f9fbb9-djftd   1/1     Running        0          107s
kubernetes-bootcamp-69b6f9fbb9-nkbr4   1/1     Running        0          111s
```

### 5.5 Annuler l'opération précédente

Voici le code :

``` bash
kubectl rollout undo deployments/kubernetes-bootcamp
```

On voit que le pods présentant une erreur a disparu.

### 5.6 Roll bacj jusqu'à la partie 2

?

## Partie 6 - Déployer une app en utilisant YAML

### 6.1 Nettoyer ce que l'on a fait

Avec les commandes suivantes :

``` bash
kubectl delete service kubernetes-bootcamp
kubectl delete deployment kubernetes-bootcamp
```

### 6.2 Modifier `To complete #1`

Voici ce que l'on ajoute :

``` bash
image: gcr.io/google-samples/kubernetes-bootcamp:v1
```

### 6.3 Appliquer `deployment.yaml`

En utilisant la commande suivant :

``` bash
kubectl apply -f deployment.yaml
```

On voit alors l'état des pods :

``` bash
NAME                                   READY   STATUS    RESTARTS   AGE
kubernetes-bootcamp-855d5cc575-bm5c4   1/1     Running   0          5s      
```

### 6.4 Modifier `service.yaml`

Voici le nouveau code :

``` yaml
apiVersion: v1
kind: Service
metadata:
  name: kubernetes-bootcamp-service
spec:
  type: NodePort
  selector:
    app: kubernetes-bootcamp
  ports:
    - port: 8080
```

### 6.5 Appliquer `service.yaml`

On utilise ceci :

``` bash
kubectl apply -f service.yaml
```

On peut maintenant accéder sur le web.

### 6.6 Modifier `To complete #2`

Voici le nouveau code 

``` yaml
replicas: 3
```

### 6.7 Lancer le code

Avec cette commande : 

``` bash
kubectl apply -f deployment.yaml
```

On voit que :

``` bash
NAME                                   READY   STATUS    RESTARTS   AGE
kubernetes-bootcamp-855d5cc575-bm5c4   1/1     Running   0          5s
kubernetes-bootcamp-855d5cc575-m49cn   1/1     Running   0          6s
kubernetes-bootcamp-855d5cc575-n8vpw   1/1     Running   0          5s
```

Si on rafraichi la page on tombe sur plusieurs répliques.