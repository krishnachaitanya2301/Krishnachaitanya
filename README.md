# Deploying application locally using Docker compose
## Steps Included to delpoy application on Docker Compose
### We will be using
 1. 2 application nodes
 2. Flask as a web application
 3. Nginx as http web server and uWSGI as our applicatio server 
 4. Redis for storage
 5. Consul to do service discovery 
 6. Docker and Docker compose 
 
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

##### Create a new directory by using following commmand:
   mkdir app && cd app
##### Inside of our app parent directory, we're going to create a couple more directorires, one for each container - flask and nginx:
mkdir flask nginx 
##### Again inside our app parent directory, create redis for storage:
mkdir redis 
##### Now create docker-compose.yml file in the app directory. We will use this file to define our application services:
touch docker-compose.yml
##### Move into flask directory :
cd flask
##### now create a virtual environment:
python -m venv env
##### Activate it:
source env/bin/activate
##### to install redis, Consul, Docker engine and docker compose :
Go to official sites respectively and use respective commands to install it
##### to install flask and uwsgi use following command:
pip install flask uwsgi
##### Once the packages are installed we will generate requirements.txt:
pip freeze  > requirements.txt
##### Now we can build our app. Create a file called run.py:
touch run.py
##### Open up run.py and add the following:
from app import app

if __name__ == "__main__":
    app.run()
##### For uWSGI configuration, create app.ini and add the following
[uwsgi]
wsgi-file = run.py
callable = app
socket = :8080
processes = 4
threads = 2
master = true
chmod-socket = 660
vacuum = true
die-on-term = true
##### We need a directory for our application, create new directory app and move into it:
mkdir app && cd app
##### Create app.py. Open it and add the following:
from flask import Flask

app = Flask(__name__)

from app import views
##### Now create views.py file and add the following:
from app import app
import os

@app.route("/")
def index():

    # Use os.getenv("key") to get environment variables
    app_name = os.getenv("APP_NAME")

    if app_name:
        return f"Hello from {app_name} running in a Docker container behind Nginx!"

    return "Hello from Flask"
    




