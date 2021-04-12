# Deploying application locally using Docker compose
## Steps Included to delpoy application on Docker Compose
### We will be using
 1. 2 application nodes
 2. Flask as a web application
 3. Nginx as http web server and uWSGI as our applicatio server 
 4. Redis for storage
 5. Consul to do service discovery 
 6. Docker Engine and Docker compose 
 
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
  
##### Now test the Flask app locally, move back to the flask directory:
    cd
    export FLASK_APP=run.py
    export FLASK_ENV=development
##### Now run the app :
    flask run
##### Hit URL in your browser and you will see
Hello From Flask
##### Now go in flask directory and create docker file:
    touch Dockerfile
##### Add the following
        # Use the Python3.8 image
    FROM python:3.8-stretch

        # Set the working directory to /app
    WORKDIR /app

        # Copy the current directory contents into the container at /app 
    ADD . /app

        # Install the dependencies
    RUN pip install -r requirements.txt

        # run the command to start uWSGI
    CMD ["uwsgi", "app.ini"]
##### The ADD . /app instruction in our Dockerfile is going to copy everything in the flask directory into the new container, but we don't need it to copy our virtual environment or any cached Python files, so to prevent this we will create .dockerignore file:
    touch .dockerignore
##### Add the following:
    env/
    __pycache__/

##### Now move to Nginx directory:
    cd ../nginx/
##### now create new file called nginx.conf:
    touch nginx.conf
##### Open this file and add the following:
    server {

       listen 80;

       location / {
           include uwsgi_params;
           uwsgi_pass flask:8080;
        }

    }
##### Go to nginx directory and create docker file:
    touch Dockerfile
##### Open this file and add the following:
         # Use the Nginx image
    FROM nginx

         # Remove the default nginx.conf
    RUN rm /etc/nginx/conf.d/default.conf

         # Replace with nginx.conf
    COPY nginx.conf /etc/nginx/conf.d/
##### Open up docker-compose.yml file and add the following:
     version: "3.7"

     services:

     flask:
        build: ./flask
        container_name: flask
        restart: always
        depends_on: 
          -redis
        environment:
          - APP_NAME=MyFlaskApp
        expose:
          - 8080

      nginx:
        build: ./nginx
        container_name: nginx
        restart: always
        ports:
          - "80:80"

      redis:
      image: redis

##### To build the services run the following command:
    docker-compose build
##### Now run the following to create and start the container:
    docker-compose up
##### Now open http://localhost/ to see your flask app in action you can see:
Hello from MyFlaskApp running in a Docker container behind Nginx!
    



