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
---
### 2) Le `Dockerfile` présent dans le dossier `hello-world` n'est pas fonctionnel. Expliquer l'ensemble des erreurs qui ont été faites.
 ---
### 3) Proposez une modification du Dockerfile afin que celui-ci soit fonctionnel.
---
### 4) Donner la commande permettant de construire l'image de l'application nodejs avec comme nom `node -hello-world:<VOTRE_NOM>` à partir du Dockerfile. **Pensez à bien expliquer chaque element de la commande.**
---
### 5) Donner la commande permettant de créer un conteneur à partir de l'image précédemment créée et de rendre accessible l'application nodejs sur le port `8080` de votre hôte (votre ordinateur portable). Essayez d'accéder à l'application sur ce port afin de vous assurer que l'application est fonctionnel.
---
### 6) L'application tourne par défaut sur le port `3000`. Vous pouvez modifier ce port en configurant la variable d'environnement `PORT` avec la valeur que vous souhaitez. Modifier la commande précédente afin de faire tourner l'application sur le port `2000`. L'application devra cependant toujours être accessible sur le port `8080` depuis l'hôte. 
---
### 7) Faire un schéma réseau expliquant le parcours d'une requête vers l'application NodeJS depuis l'hôte. Vous préciserez les numéros de port de l'hôte, et du conteneur. Décrire le schéma à l'aide d'un court texte.

---

## B) Orchestration de conteneur avec Kubernetes

Le dossier `kubernetes` contient les ressources Kubernetes permettant de déployer et de rendre accessible l'application nodejs sur Kubernetes.

Nous supposerons qu'un cluster Kubernetes a été déployé sur Azure en utilisant le service AKS.

Sur ce cluster, un Ingress Controller Nginx a été configuré avec une `ingressclass` ayant comme valeur `nginx`. L'ingress controller est rattaché à un service de type `LoadBalancer` avec comme nom de domaine (DNS) `devops-ynov.com` qui permet de rediriger toutes les requêtes vers ce domaine et ses sous-domaines (`*.devops-ynov.com`) vers l'Ingress Controller.

### 1) Expliquer le rôle de chacune des ressources en détaillant leur fonctionnement et la manière dont elles interagissent entre elles.
---
### 2) Il y a des erreurs dans la définition des ressources Kubernetes. Trouvez ces erreurs, expliquer les et proposez un correctif. Le but est de pouvoir exposer l'application Nodejs à l’extérieur du cluster et de pouvoir y accéder à partir de l'adresse suivante `hello-world.devops-ynov.com`.
---
### 3) Faire un schéma qui expliquant le parcours d'une requête du client de l'utilisateur jusqu'à l'application NodeJS déployé dans le cluster Kubernetes. Vous prendrez le soin d'expliquer les détails de chaque étape de transition (ingress controller, service, pods) et précisant les numéros des ports.

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


### 2) Expliquer en quoi un outil comme Kubernetes permet de faciliter la mise en place d'un environnement DevOps dans une équipe.





