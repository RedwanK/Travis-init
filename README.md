# TP1
## Database

1 -  Move in to /database (or wherever your db Dockerfile is)
```
$ cd database/
```

2 -  Then build the db dockerfile
```
$ docker build -t redwan/postgres .
```
Here you build the Dockerfile of current folder (.) and you call it as you want (redwan/postgres)

3 -  Run the previously mounted image
```
$ docker run -p 5432:5432 --name postgres -v ~/Documents/00-DEVOPS/TP1/database/datadir:/var/lib/postgresql/data  redwan/postgres
```
Here you use the ports which fits to your needs (5432 is postgres's default).
Then you give the container a name (here postgres, cause it's just that).
-v Option means that you link your system's folder to the container datas to save them.
And you tell it to use your image (redwan/postgres).

4 - Now you have an up and running postgres container you need to verify it. Lets run an adminer.
```
$ docker run --link postgres:db -p 8080:8080 adminer
```
So you create an adminer container, linked to the postgres (its the name) container you just mounted. You also give it 8080 port for both sides.

## Backend API

1 - Move in to /api/simple-api 
```
$ cd /api/simple-api
```

2 - Build the api dockerfile
```
$ docker build -t redwan/api .
```

3 - Run the container
```
$ docker run -it --rm --link postgres:db --name simpleapi -p 8080:8080 redwan/api
```
Here you run the container and link it to the postgres container in order to let your api ask your container

4 - test the api from a browser
```
http://localhost:8080/departments/IRC/students

[{"id":1,"firstname":"Eli","lastname":"Copter","department":{"id":1,"name":"IRC"}}]
```

## HTTP Server

1 - Move in to /http-server
```
$ cd /http-server
```

2 - Create a basic html file + apache config file for a reverse proxy

3 - Create the Dockerfile
```
FROM httpd:2.4
COPY ./public-html/ /usr/local/apache2/htdocs/
```

4 - Build the image
```
$ docker build -t redwan/http-server .
```

5 - Run the container linking the api container
```
$ docker run --rm --link simpleapi --name apache -p 85:80 redwan/http-server
```

# TP2

## Setup
---

We use to split the tasks by using stages java and nodejs, Travis detects maven through the pom.xml file.

Yaml file configuration Travis CI (.travis.yml), GitLab CI (.gitlab-ci.yml).
Jenkins uses Groovy files.

The file pom.xml tells java stage to use maven and gives configuration instruction.

Units tests are here to test each method or service of an app with assertions.
Integrations tests are here to check if the project setup successfully and every component (database, http server, api...) work fine.

Tests containers are java libraries which facilitate the use of docker in java.

## Checkpoint : Working CI :

1. init a new GitHub repo
2. Link GitHub and Travis 
3. import repo into Travis
4. create .travis.yml
5. Commit / push

## First steps into CD world
---

We need a develop branch not to deploy the project on prod so we tell travis not to execute some routines unless current branch is not tagged or specific name or whatever. 

We use secured variables for security of course, I'll not put my ssh private key on a github public repo...

To keep the latest version of the environment up and running any time. You modify something, you push the new docker image and your mate can install the project easily.

## Checkpoint CD : Quality Gate :

1. Import repo on SonarCloud
2. change .travis.yml accordingly
3. Commit / push

## Checkpoint CD : splitted pipeline :

We just create a new stage for each job we give to Travis. Of course, like you would not create 500 lines methods of php code, you split your code to reuse it.

# TP 3

## Intro

The /api/actuator endpoint is a common spring boot endpoint. It gives informations to monitor all routes of the api.

for example : 
```
http://localhost:82/api/actuator

{
    "_links":{"self":{"href":"http://localhost:82/api/actuator","templated":false},
    "beans":{"href":"http://localhost:82/api/actuator/beans","templated":false},
    "health":{"href":"http://localhost:82/api/actuator/health","templated":false},
    "health-component":{"href":"http://localhost:82/api/actuator/health/{component}","templated":true},
    "health-component-instance":{"href":"http://localhost:82/api/actuator/health/{component}/{instance}","templated":true},
    "configprops":{"href":"http://localhost:82/api/actuator/configprops","templated":false},
    "env-toMatch":{"href":"http://localhost:82/api/actuator/env/{toMatch}","templated":true},
    "env":{"href":"http://localhost:82/api/actuator/env","templated":false},
    "info":{"href":"http://localhost:82/api/actuator/info","templated":false},
    "prometheus":{"href":"http://localhost:82/api/actuator/prometheus","templated":false},
    "metrics":{"href":"http://localhost:82/api/actuator/metrics","templated":false},
    "metrics-requiredMetricName":{"href":"http://localhost:82/api/actuator/metrics/{requiredMetricName}","templated":true}}
}
```

$basearch. You can use $basearch to reference the base architecture of the system. For example, i686 machines have a base architecture of i386 , and AMD64 and Intel 64 machines have a base architecture of x86_64

## Inventories

The inventory file is here to set the env variable (domain name for example)

this is the command to execute ansible (ping) with an inventory

```
ansible all -i inventories/setup.yml -m ping
```

## Facts

We can execute ansible tasks with the command line using an inventory as admin :
```
ansible all -i inventories/setup.yml -m yum -a "name=httpd state=absent" --become
```
Here we remove httpd from the remote server

## docker_container

Take out coniguration from docker_compose and translate if you have one, using ansible documentation its quite easy. If you don't have any docker-compose file this is an example of task using docker_container :
```
- name: Create Database
  docker_container:
      name: database
      image: postgres:12.0-alpine
      state: started
      networks:
          - name: global_network
      env:
          POSTGRES_PASSWORD: takimapass
          POSTGRES_USER: takima
          POSTGRES_DB: SchoolOrganisation
```  

With ansible we can automatize a docker_compose routine remotely, basically its like you run a docker_compose in several remote servers from your computer (or automatically with travis or jenkins) 

## Continuous deployment

I created a dockerfile with a debian distribution, where I installed ansible and all required stuff, then I just call my playbook to deploy the app.
Once I have this docker image i push it to docker hub and then I tell travis (into .travis.yml) to build and run this image which will deploy my app, only on master for example. 