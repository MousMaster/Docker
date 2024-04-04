

### Inntroduction

Dans le contexte de ce projet, dont les spécifications sont disponibles  [ici](https://github.com/diranetafen/student-list.git "here"). 

J'ai conceptualisé une architecture Docker pour faciliter le déploiement et la gestion de l'application.

L'application en question se compose de deux modules principaux:
 
1: Une API REST (nécessitant une authentification basique) qui  renvoie une liste d'étudiants à partir d'un fichier JSON.

2: Un frontend en PHP et HTML qui interagit avec cette API.
 
Pour une meilleure organisation, j'ai créé deux réseaux Docker distincts. Le premier est dédié à l'application elle-même, tandis que le second est réservé au déploiement d'un registre Docker local.

Un aperçu de l'architecture proposé est illustré ci-dessous.

![project](https://github.com/MousMaster/Docker/blob/master/diagramme_infrastructure.png)


------------

### Plan d'action
  

Mon approche est de dockeriser l'API en premier, puis de déployer l'ensemble de l'infrastructure via un fichier docker-compose.


### Étape 1: Création de l'image de l'API

Apres avoir creé le dockerfile on la lance les commandes suivantes : 

```console
docker build -t student-list-api-image . student-api 
```
*Cette commande génère l'image Docker.*

```console
docker run -v ./student_age.json:/data/student_age.json -p 8082:5000 student-api 
```

*Cette commande déploie l'image sous forme de conteneur, en lui associant un volume et en exposant le port 8082. *


```console
 curl -u toto:python -X GET http://127.0.0.1:50000/pozos/api/v1.0/get_student_ages
 ```

 *Cette commande teste la connexion à l'API. Les identifiants "toto" et "python" sont utilisés pour l'authentification HTTP basique.*
 *

![project](https://github.com/MousMaster/Docker/blob/master/images/curl_ok.png)

On test a present si l'api repond et renvoie la liste des étudiants, confirmant ainsi le bon fonctionnement de l'API.





*On arrive a reccuperer la list des etudiants on en conclue que l'api est fonctionnelle*


### Étape 2: Mise en place du docker-compose



Après avoir généré le fichier docker-compose, on modifie la ligne :

```console
$url = 'http://<api_ip_or_name:port>/pozos/api/v1.0/get_student_ages';

```
par :
           
```console
$url = 'http://student-api:5000/pozos/api/v1.0/get_student_ages';
```

Cela permet au frontend de pointer vers le backend. Remplacez <api_ip_or_name> par le nom du conteneur (student-api) et <port> par 5000.

Puis on test si l'on arrive a reccuperer la liste des etudiants a partir du frontend :

![project](https://github.com/MousMaster/Docker/blob/master/images/we_site_ok.png)
  

### Étape 3: Déploiement d'un registre Docker local


Pour cette étape, j'ai opté pour une approche "Infrastructure as Code". Une fois le registre lancé,j'ai lancé les commandes suivantes : 

```console
docker image tag student-list-api localhost:51000/v2/student_api
```

* Cette commande tag l'image, étape nécessaire avant de la charger sur un registre Docker. *

```console
docker push localhost:51000/v2/student_api                      
```
* Cette commande charge l'image créée précédemment sur le registre Docker local.
 *



![project](https://github.com/MousMaster/Docker/blob/master/images/push_ok.png)

![project](https://github.com/MousMaster/Docker/blob/master/images/push_front_ok.png)

Les captures d'écran fournies montrent que les étapes se sont déroulées avec succès.


Lien pour tester l'infra :

Registre : http://52.6.33.255:80
Student UI : http://52.6.33.255:8000


[![Docker Compose Workflow](https://github.com/MousMaster/DockerProjet/actions/workflows/main.yml/badge.svg)](https://github.com/MousMaster/DockerProjet/actions/workflows/main.yml)

[Return to portfolio](https://github.com/MousMaster/Portfolio)




