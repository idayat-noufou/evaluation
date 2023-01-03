# Evaluation

**Pensez à bien expliquer chaque argument dans les commandes que vous donnerez dans l’évaluation. Une commande non expliquée ne permettra pas d'avoir l'ensemble des points de la question.**

---
## A) Conteneurisation avec Docker 

Le dossier `hello-world` contient le code d'une application nodejs.

Rappels :

- `yarn install` permet d'installer les dépendances d'une application nodejs dans le dossier `node_modules`
- `yarn build` génère les fichiers javascript dans le dossier `dist` qui seront interprétés par le moteur nodejs.
- `node dist/main.js` permet de démarrer l'application (pensez que le chemin vers `dist/main.js` est relatif à répertoire où la commande est exécutée).
- Une fois démarrée, l'application tourne part défaut sur le port `3000`.


### 1) Expliquer quel est le rôle d'un Dockerfile.
 Le dockerfile sert à rassembler toutes les commandes nécessaires au build d'une image.
### 2) Le `Dockerfile` présent dans le dossier `hello-world` n'est pas fonctionnel. Expliquer l'ensemble des erreurs qui ont été faites.
Le nom de l'image est *ubuntu* alors que node n'y est pas installé, il faut soit changer le nom de l'image soit installer node
l'api se trouve dans le dossier hello-word qui est le dossier courant du Dockerfile or le copy du Dockerfile copie les fichiers du dossier parent

### 3) Proposez une modification du Dockerfile afin que celui-ci soit fonctionnel.
```Dockerfile
FROM node:alpine 
WORKDIR /app #on définit un dossier de travail( la commande COPY renverra donc au dossier parent qui est hello-world) 
COPY . .
RUN yarn install
RUN yarn build
CMD node /app/dist/main.js
```
### 4) Donner la commande permettant de construire l'image de l'application nodejs avec comme nom `node -hello-world:<VOTRE_NOM>` à partir du Dockerfile. **Pensez à bien expliquer chaque element de la commande.** 
```bash 
docker build ./hello-world -t node-hello-world:idayat # docker build est la commande permettant de construire l'image;'./hello-word' est le path ou se trouve le Dockerfile vu que j'exéctute la commande dans le dossier evaluetion; -t fait référence au tag qui est sous la forme image:tag dans notre cas : nom de l'image = node-hello-world et nom du tag = idayat
```  
### 5) Donner la commande permettant de créer un conteneur à partir de l'image précédemment créée et de rendre accessible l'application nodejs sur le port `8080` de votre hôte (votre ordinateur portable). Essayez d'accéder à l'application sur ce port afin de vous assurer que l'application est fonctionnel.
```bash 
docker run node-hello-world:idayat -p 8080:3000
# docker run permet de créer un conteneur à partir d'une image et 'node-hello-world:idayat' est le nom de l'image à partir de laquelle est crée le conteneur -p 8080:3000 permet de rediriger l'activité de l'api qui tourne sur le port 3000 (d'après la question suivante) vers le port 8080 en local.
```  
### 6) L'application tourne par défaut sur le port `3000`. Vous pouvez modifier ce port en configurant la variable d'environnement `PORT` avec la valeur que vous souhaitez. Modifier la commande précédente afin de faire tourner l'application sur le port `2000`. L'application devra cependant toujours être accessible sur le port `8080` depuis l'hôte. 
```bash 
docker run node-hello-world:idayat -p 8080:2000
# on change la valeur correspondant au port pour modifier la configuration et on garde le port 8080
```
### 7) Faire un schéma réseau expliquant le parcours d'une requête vers l'application NodeJS depuis l'hôte. Vous préciserez les numéros de port de l'hôte, et du conteneur. Décrire le schéma à l'aide d'un court texte.
nom de l'image : A_7 schéma de conteneurisation de l'api.png
description du schéma: le client(mon ordinateur local) intéroge l'api sur le port 8080 (donc sur localhost:8080) qui redirige la requête vers le conteneur docker de l'api qui tourne sur le port 200 du conteneur.

![schémas/A_7 schéma de conteneurisation de l'api.png]()

## B) Orchestration de conteneur avec Kubernetes

Le dossier `kubernetes` contient les ressources Kubernetes permettant de déployer et de rendre accessible l'application nodejs sur Kubernetes.

Nous supposerons qu'un cluster Kubernetes a été déployé sur Azure en utilisant le service AKS.

Sur ce cluster, un Ingress Controller Nginx a été configuré avec une `ingressclass` ayant comme valeur `nginx`. L'ingress controller est rattaché à un service de type `LoadBalancer` avec comme nom de domaine (DNS) `devops-ynov.com` qui permet de rediriger toutes les requêtes vers ce domaine et ses sous-domaines (`*.devops-ynov.com`) vers l'Ingress Controller.

### 1) Expliquer le rôle de chacune des ressources en détaillant leur fonctionnement et la manière dont elles interagissent entre elles.
* ressource 1 : deployment , elle sert à créer et gérér un nombre donné de pods (3 dan snotre cas) contenant chacun un conteneur de l'api `hello-word` crée par l'image `yrez/node-hello-world`.

* ressource 2 : service , elle permet de communiquer avec les pods. Vu que le type de ce service est LoadBalancer, cette ressource se chargera de répartir les requêtes entre les différents pods.

* ressource 3 : ingress , elle permet de rediriger les requêtes qui arrivent du client vers les noeuds disponiples.
### 2) Il y a des erreurs dans la définition des ressources Kubernetes. Trouvez ces erreurs, expliquer les et proposez un correctif. Le but est de pouvoir exposer l'application Nodejs à l’extérieur du cluster et de pouvoir y accéder à partir de l'adresse suivante `hello-world.devops-ynov.com`.
*note : les points de suspension sont le voleurs inchangées dans le fichier original*
* modification dans deployment.yaml
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  ...
spec:
  ...
  template:
    metadata:
      labels:
        app: hello-world #on remplace cette valeur application: hello-world
    ...
```

* modification dans service.yaml
```yaml
apiVersion: v1
kind: Service
metadata:
  name: hello-world-service #name: hello-world #ce nom ne correspond pas au nom de service donné dans l'ingress
spec:
  #le selector n'a pas été donné donc service ne pourra pas être lié aux pods de l'api
  selector:
    app: hello-world
  ports:
    - #name: api  #cette info ne semble pas nécessaire
      protocol: TCP
      port: api #port: 80 #il doit correspondre au nom donnée dans le deployment.yaml
      targetPort: 2000
  selector:
    name: hello-world
  type: LoadBalancer
```

* modification dans ingress.yaml
```yaml
apiVersion: networking.k8s.io/v1
...
spec:
  ingressClassName: traefik
  rules:
    - host: hello-world.devops-ynov.com #host: hello-world.com #l'adresse du host ne correspond pas à celle à laquelle on souhaite accéder.
      ...
```
### 3) Faire un schéma qui expliquant le parcours d'une requête du client de l'utilisateur jusqu'à l'application NodeJS déployé dans le cluster Kubernetes. Vous prendrez le soin d'expliquer les détails de chaque étape de transition (ingress controller, service, pods) et précisant les numéros des ports.

![schémas/B_3 schéma kubernitie de l'api.png]()

## C) CI/CD

Vous êtes le responsable d'une équipe qui développe un site web de eCommerce. Cette application est     actuellement en production. L'application est développée en NodeJS et est déployé sur un cluster Kubernetes.

Cependant les étapes concernant la mise en production d'une nouvelle version de l'application sont réalisées manuellement.


### 1) Lister l'ensemble des étapes nécessaires entre la modification du code source par un développeur jusqu'au déploiement d'une nouvelle version de l'application en production, (n'oubliez pas que l'application est déployée sur Kubernetes). Pensez également aux étapes liées à la qualité de l'application. Décrire chacune de ces étapes en expliquant leur but.
---
### 2) Expliquer ce qu'est la CI/CD et en quoi elle permet d'aider à l'automatisation du cycle de développement de l'application, jusqu'à la mise en production.
---
### 3) Réaliser un schéma représentant un pipeline de CI/CD reprenant les différentes étapes que vous avez listées dans la question 1) qui permettait de déployer une nouvelle version de l'application en production.


## D) Culture DevOps

### 1) Expliquer en quoi la conteneurisation et Docker permettent de faciliter la mise en place d'un environnement DevOps dans une équipe.
Grâce à la conteneurisation et docker, les développeurs, ont la mains sur les configurations et les applications. les outils nécessaires aux développements sont basés sur ceux utilisés en production, ce qui permet d'éviter les erreurs pendant le déploiement.

### 2) Expliquer en quoi un outil comme Kubernetes permet de faciliter la mise en place d'un environnement DevOps dans une équipe.
Kubernetes permet facilement de mettre en place un environnement de de déploiement d'une application. il permet donc pour le développeur de travailler dans un environnement de production mis en place par lui-même ou par les personnes spécialisées, et de constater les potentiels bug. 





