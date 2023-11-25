#Flask Calculator Web Application
**Introduction**

This documentation provides an overview of the Flask calculator web application and guides you on how to set up and run the project. The Flask calculator is a straightforward web application designed for basic arithmetic calculations, including addition, subtraction, multiplication, and division.

**Tools Requirements**

Python | installation guide [Click Here](https://kinsta.com/knowledgebase/install-python/) 

Docker | installation guide [Click Here](https://docs.docker.com/engine/install/ubuntu/)

### Getting Started
Open your terminal and clone the Flask Calculator Web Application repository using the following command:

```
git clone https://github.com/cloudspaceacademy/Flask-Calculator-app.git
```
Navigate to the **"Flask-Calculator-app"** directory By running this command :


```cd Flask-Calculator-app```


Inside the **"Flask-Calculator-app"** directory, you will find a **"cloudspace"** directory. Enter this directory using the following command:

```cd cloudspace```

In Previous session **"Docker Beginner"** you have learned about how to run this **"Flask Calculator Web Application"** using **Dockerfile**.Now we are gonna learn how to run this application using **docker-compose.yml file**.

In Previous session **"Docker Beginner"** you have learned about **Dockerfile**.How To write a Dockerfile,why its used for,how to run the application using **Dockerfile** etc.Now let's jump up to **docker-compose.yml** file.

### docker-compose.yml file used for 

A docker-compose.yml file is used to define a multi-container Docker application. It allows you to define and configure multiple services, networks, and volumes in a single file. Docker Compose simplifies the process of orchestrating complex applications by providing a way to manage multiple containers as a single application stack.

Simply **Dockerfile** is used for single service or to run the application using one container and **docker-compose.yml** file is used for multiple services or to packaging and run the application using multi containers.

### docker-compose.yml

```
version: '3.8'

services:
  flask-calculator:
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - "8080:80"

```

Let's Break down the code 

**version: '3.8'**:Specifies the version of the Docker Compose file syntax. In this case, it's using version 3.8.

**services**:Defines the services that make up the Docker application.

**flask-calculator**:The name of the service. In this case, it's named flask-calculator.

**build**:Specifies how to build the Docker image for the service.

**context: .** :Specifies the build context, which is the path to the directory containing the Dockerfile and any files needed for the build. In this case, it's set to the current directory (.)

**dockerfile: Dockerfile**:Specifies the name of the Dockerfile to use for building the image. In this case, it's named Dockerfile.

**ports**:Specifies the ports to expose from the container.
- "8080:80":

Maps port 80 from the container to port 8080 on the host machine. The format is host_port:container_port.

## Running the Application with Docker Compose

To run the application run this command where the **docker-compose.yml** file is located.

Command :


```docker-compose up -d``` 

Break down Codes:

**docker-compose**: The Docker Compose command-line tool.

**up**: This command is used to create and start containers based on the configurations specified in the docker-compose.yml file.

**-d**: Stands for "detached mode." When you run Docker containers in detached mode, the containers run in the background, and you get your terminal prompt back. This allows you to continue using the same terminal session for other tasks.


Now Run ```docker ps``` command to see the running container id that you have just created.

## Access the application 
Now you will see the app is running from your local machine on port 8080.
Open your web browser and go to **localhost:8080** to access the Flask calculator web application within the Docker container.