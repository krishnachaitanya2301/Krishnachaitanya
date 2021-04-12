# Deploying application locally using Docker compose
## Steps Included to delpoy application on Docker Compose
### We will be using
 1. 2 application nodes
 2. Flask as a web application
 3. Nginx as http web server and uWSGI as our applicatio server 
 4. Redis for storage
 5. Consul to do service discovery 
 6. Docker and Docker compose 
 After installing all this we need to start deploying our application on Docker Compose.
 
 ## Application Structure
 app
├── docker-compose.yml
├── flask
│   ├── Dockerfile
│   ├── .dockerignore
│   ├── app
│   │   ├── __init__.py
│   │   └── views.py
│   ├── app.ini
│   ├── requirements.txt
│   └── run.py
├── nginx
│   ├── Dockerfile
│   └── nginx.conf
├── redis
└── readme.md

##### Create a new directory by using following commmand 
   mkdir app && cd app

##### Inside of our app parent directory, we're going to create a couple more directorires, one for each container - flask and nginx:

mkdir flask nginx 

##### Now create docker-compose.yml file in the app directory. We will use this file to define our application services

touch docker-compose.yml

##### 
