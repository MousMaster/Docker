

### Inntroduction

Dans le cadre de ce projet, dont les spécifications sont disponibles [ici](https://github.com/diranetafen/student-list.git "here"). 

J'ai opté pour la création de deux réseaux distincts afin de mieux structurer mon environnement Docker. Le premier réseau est dédié à l'application que je souhaite dockeriser, composée d'un backend en Python et d'un frontend en PHP. Le second réseau est réservé au déploiement local d'un registre Docker, comprenant une api et son interface utilisateur.

Un aperçu de l'architecture proposé est illustré ci-dessous.

![project](https://github.com/MousMaster/Docker/blob/master/diagramme_infrastructure.png)


------------

### Mon plan  

La procédure initiale consiste à créer l'image Docker puis de deployer l'enssemble de l'infrastructure à l'aide d'un fichier docker-compose.





### Etape 1: creation de l'image de l'api


Apres avoir creer le dockerfile on la lance les commandes suivantes : 

```console
docker build -t student-list-api-image . student-api 
```
*cette commande permet la generation de l'iamge "

```console
docker run -v ./student_age.json:/data/student_age.json -p 8082:5000 student-api 
```

*cette commande permet le deploiement de l'image sous forme de conteneur en lui affectant un bind volume contenant la list des etudiants et en exposant le port 8082 *


```console
 curl -u toto:python -X GET http://127.0.0.1:50000/pozos/api/v1.0/get_student_ages
 ```

 *commande permettant de faire le test de connexion a l'api , toto et python etant les arguments pour pouvoir se connecter en authentification HTTP basique. 
 *


On test a present si l'api repond 
![project](https://github.com/MousMaster/Docker/blob/master/images/curl_ok.png)

*On arrive a reccuperer la list des etudiants on en conclue que l'api est fonctionnelle*


### Etape 2 : generation du docker-compose pour le depoiement de l'enssemble de l'infrastructure


Apres avoir genere le fichier docker-compose , il faut modifier la ligne 

```console
$url = 'http://<api_ip_or_name:port>/pozos/api/v1.0/get_student_ages';

```
en 
              
```console
$url = 'http://student-api:5000/pozos/api/v1.0/get_student_ages';
```
pour que le frontend pointe sur le backend pour cela il faut remplacer api_ip par le nom du conteneur ici c'est student-api et port par 5000.

Puis on test si l'on arrive a reccuperer la liste des etudiants a partir du frontend :

![project](https://github.com/MousMaster/Docker/blob/master/images/we_site_ok.png)
  

### Etape 3 : deploiement d'un registre docker en local 

Dans cette derniere etape au lieu de deployer le regisre en avec docker run j'ai choisi de le faire en interface as code comme explique dans l'introduction.
Apres avoir lance le registre j'ai lance les commandes suivantes : 

```console
docker image tag student-list-api localhost:51000/v2/student_api
```

* commande permettant de taguer une image , etape obligatoire avant l'envoie sur un registre docker *

```console
docker push localhost:51000/v2/student_api                      
```
* commande permettant de charger l'iamge cree precedement sur le registre docker local *

![project](https://github.com/MousMaster/Docker/blob/master/images/push_ok.png)



* image montrant que le chargement de l'image s'est bien passe *

![project](https://github.com/MousMaster/Docker/blob/master/images/push_front_ok.png)