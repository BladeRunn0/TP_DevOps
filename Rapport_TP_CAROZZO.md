# Rapport TP DevOps

## TP1 - Docker

Au cours de ce TP, nous utilisons Docker grâce à des Dockerfile. 
En effet, Docker permet de créer, déployer et exécuter des applications dans 
des __conteneurs__. Les conteneurs offrent :
- _portabilité_ - toutes les applications peuvent
s'éxécuter n'importe où à condition d'avoir Docker -  
- _cohérence_ - les environnements de dev,
test et production restent cohérents entre eux -
- _isolation_ - chaque application s'exécute
de manière isolée afin de ne pas provoquer dinterférences avec d'autres -

ce qui simplifie le développement, le test et le déploiement d'applications. 

L'utilisation de Docker et des Dockerfiles facilite la gestion des environnements d'exécution
et améliore la fiabilité du déploiement des applications.

### Database

Dans cette partie, nous créons une base de données que nous allons initialiser puis faire 
tourner grâce à Docker. Pour la visualiser nous utilisons l'application Adminer.

Tout d'abord, le script Dockerfile ressemble à :
`FROM postgres:14.1-alpine

ENV POSTGRES_DB=db \
POSTGRES_USER=usr \
POSTGRES_PASSWORD=pwd`

Dans ce dernier, on déclare la version de système de base de données à utiliser. On renseigne 
ensuite les variables nécessaires au fonctionnement de la base de données.

Ensuite, et pour faciliter les interactions entre les diverses parties de l'application finale, 
on crée un réseau commun entre elles : `docker network create app-network`.

Enfin, à fin de visualiser la base de données on exécute : 
`docker run -p "8090:8080" --net=app-network --name=adminer -d adminer`. Cette commande permet de démarrer Adminer.

Il ne reste plus qu'à lancer le container de la base de données :
`docker run --network app-network -p 8080:8080 --name tp_database bladerunner0/tp_database`
Il est possible d'ajouter `-d` à la commande pour ne pas voir toute la liste de logs de la commande.
Une fois la base lancée, il suffit d'aller sur `localhost:8090` pour y accéder. On arrive sur la page Adminer.

Sur la page login d'Adminer, afin de visualiser véritablement les données, il faut rentrer :
- Système : PostgreSQL
- Serveur : tp_database
- Utilisateur : usr
- Mot de passe : pwd
- Base de données : db

#### Attention

Pour appeler les fichiers SQL à utiliser dans le Dockerfile, on doit y écrire : `COPY <YOUR_FILE>.sql /docker-entrypoint-initdb.d`.
Cela permet d'implémenter ces fichiers. Cependant, il faut faire attention car les fichiers seront, à
l'exécution, appelé dans l'ordre __alphabétique__.

### Backend

Pour le backend de l'application, on utilise des fichiers écrits en Java. Ensuite, on crée un
Dockerfile contenant les informations nécessaires à la création et au fonctionnement du container, similaires
à la base de données.
Cependant, ce fichier contient deux blocs d'instructions : un pour construire et un autre pour lancer
le backend de l'application.

Une fois le container lancé, on peut vérifier le fonctionnement en se rendant sur `localhost:80801:departments/IRC/students`.
On obtient alors un affichage JSON avec un étudiant.

#### Multistage
Le build en multistage permet à l'utilisateur d'effectuer plusieurs commandes dans un seul fichier. Par exemple, avec un fichier Java, le fichier
doit être compilé puis exécuté. Avec multistage, le Dockerfile contient les instructions pour compiler et exécuter le fichier
l'utilisateur n'a donc qu'à exécuter le Dockerfile.

### HTTP Server

Dans cette partie, on crée un petit server HTTP qui va permettre d'agir en proxy sur les connexions 
entrantes et les requêtes afin de rediriger l'utilisateur sur le front-end et exécuter les requêtes du
front-end vers le back-end.

### Docker compose
Les commandes les plus importantes d'un docker-compose sont les suivantes :

- `docker-compose up` : Cette commande crée et démarre tous les services définis dans le fichier `docker-compose.yml`.

- `docker-compose down` : Cette commande arrête et supprime les conteneurs, les réseaux et les volumes définis dans le fichier `docker-compose.yml`.

- `docker-compose build` : Cette commande construit ou reconstruit les services définis dans le fichier `docker-compose.yml`.

- `docker-compose start` : Cette commande démarre les services définis dans le fichier `docker-compose.yml`.

- `docker-compose stop` : Cette commande arrête les services définis dans le `docker-compose.yml` sans les supprimer.

- `docker-compose restart` : Cette commande redémarre les services définis dans le fichier `docker-compose.yml`.

- `docker-compose pause` : Cette commande met en pause tous les services définis dans le fichier `docker-compose.yml`.

- `docker-compose unpause` : Cette commande désactive tous les services qui ont été mis en pause à l'aide de la commande `docker-compose pause`.

- `docker-compose ps` : Cette commande liste tous les conteneurs en cours d'exécution, ainsi que leur état et d'autres informations.

- `docker-compose logs` : Cette commande affiche les journaux des services définis dans le fichier `docker-compose.yml`.

#### Problème de démarrage de docker compose
Dans le fichier `docker-compose.yml`, les noms des conteneurs sont forcés. Si vous remarquez dans la console en faisant `docker compose up` que certains d'entre eux sont encore générés avec le nom forcé comme
de base, arrêtez et supprimez tous les conteneurs créés puis faites `docker-compose up --force-recreate --build`. Cette commande forcera Docker à supprimer toutes les images et à tout reconstruire à partir de zéro avant de démarrer les conteneurs.
avant de démarrer les conteneurs.


### Publication des containers
Lors du lancement de docker compose, 3 images sont créées. Pour publier sur le hub Docker, il faut taguer et publier chacune d'entre elles :
`docker tag <IMAGE_NAME> USERNAME/<IMAGE_NAME>:1.0`
`docker push USERNAME/<IMAGE_NAME>:1.0`

Il est alors possible de les visualiser sur le site Dockerhub.


## TP2 - GitHub Actions

### CI - Continous Integration

Dans cette partie, on implémente le CI qui va être utile à notre application puisque chaque
modification envoyée sur GitHub va déclencher les tests relatifs à l'application grâce au fichier `.github/workflows/main.yml`.
Ce fichier contient les diverses actions à exécuter, aussi bien les installations de dépendences 
spécifiques que les instructions de lancement des containers.

#### Que sont les testcontainers ?
`Testcontainers` est une bibliothèque Java qui permet au développeur de faciliter les tests d'intégration directement dans les conteneurs Docker.
directement dans les conteneurs Docker.
Le développeur peut créer et gérer des conteneurs pour les tests d'intégration : la création de l'environnement de test est plus facile.
est plus facile. Ces conteneurs de test ont une configuration de production.
Les tests seront fiables et complets et le code est garanti de fonctionner en situation réelle.

### CD - Continous Deployement

Dans cette partie, on permet à Github, une fois les actions du workflow effectuées et validées,
d'envoyer les containers créés sur Dockerhub afin de les publier. 
Pour cela, il ne faut pas oublier d'ajouter en clés secrètes les identifiants du compte Dockerhub du développeur
puis de bien spécifier ces clés dans le code du `.github/workflows/main.yml`. Par exemple,
`${{secrets.DOCKERHUB_USERNAME}}/tp-devops-http:latest`

### SonarCloud - Setup Quality Gate

Les Quality Gates permettent de s'assurer que le code sera maintenable et pour déterminer tous les 
blocs non sécurisés. Elle aide à produire des fonctionnalités de meilleure qualité et testées, 
et elle permet également d'éviter que du code bugué soit poussé sur la branche principale.

Pour ce faire, nous allons utiliser SonarCloud, une solution cloud qui permet d'analyser le 
code et d'établir des rapports.


## TP3 - Ansible

Ansible est une plateforme logicielle open-source facilitant l'automatisation des tâches complexes 
de gestion de la configuration, du déploiement d'applications et de l'orchestration des 
infrastructures. Il fonctionne sans agent, simplifiant ainsi la configuration et la maintenance, 
tout en garantissant la cohérence des déploiements sur différents systèmes. 
Ansible améliore donc l'efficacité opérationnelle en automatisant les processus et en 
simplifiant la gestion des configurations sur une variété de systèmes.

Dans ce TP, nous utilisons Ansible afin de déployer les containers sur un serveur pour les faire fonctionner
ensemble et donc déployer l'application sur le serveur (`louis.carozzo.takima.cloud`).

Bien que l'on puisse déclarer toutes les étapes de déploiement dans un simple fichier `playbook.yml`,
ce dernier deviendrait trop complexe. Etant donné que nous possédons des parties d'application clairement
délimitées, on utilise les "rôles" Ansible correspondant à ces parties afin de répartir les 
diverses commandes spécifiques à chaque parties. Ainsi, le `playbook.yml` se simplifie, appelant simplement
les rôles nécessaires. 

#### Attention
Il faut faire attention à l'ordre d'appel des rôles, étant donné qu'ils sont appelés dans l'ordre de
déclaration dans le fichier `playbook.yml`. Comme certains sont inter-dépendants, il faut appeler
les rôles n'ayant pas de dépendances en premier et ensuite suivre cet ordre.