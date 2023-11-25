
# Flask Calculator Web Application


**Introduction**

This documentation provides an overview of the Flask calculator web application and guides you on how to set up and run the project. This simple web application allows users to perform basic arithmetic calculations such as addition, subtraction, multiplication, and division.

**Tools Requirements**

Python | installation guide [Click Here](https://kinsta.com/knowledgebase/install-python/) 

Docker | installation guide [Click Here](https://docs.docker.com/engine/install/ubuntu/)



## Getting Started

Open terminal and run this command to clone the repository of Flask Calculator Web Application.

Command:

```
git clone https://github.com/cloudspaceacademy/Flask-Calculator-app.git
```

Now navigate to the directory Flask-Calculator-app. You will see a cloudspace directory now open that directory using the terminal. 

You can simply use this command:

```cd Flask-Calculator-app && cd cloudspace```

Now run  ```ls```  command and Now what are the scenarios there you will see app.py,Dockerfile,requirements.txt file and a directory named templates.


Let's elaborate why we use that **Dockerfile** for this application and how we write that Dockerfile.


### Why do we use docker?

Docker provides a way to package, distribute, and run applications consistently across various environments. It simplifies the process of creating, deploying, and maintaining applications while improving portability, scalability, and resource efficiency. The Dockerfile is at the heart of this process, defining how the container image is built and what software is included, making it a valuable tool for modern software development and deployment.

Now let's elaborate how we write that Dockerfile to package our application.
## Dockerfile 

```
FROM python:3.9-slim

WORKDIR /app

COPY requirements.txt .

RUN pip install -r requirements.txt

COPY . .

EXPOSE 5000

CMD ["python", "app.py"] 
```
Now just break down all the code and elaborate why they use for 

**FROM python:3.9-slim**

This line specifies the base image for our Docker container. In this case, it's using the official Python 3.9 slim image. The "slim" variant provides a smaller image size by excluding some non-essential components, making it suitable for many Python applications.


**WORKDIR /app**

This line sets the working directory inside the container to /app. This is where the application code and files will be placed, and it's the directory where commands will be executed by default. It's a good practice to organize your application within a dedicated directory.


**COPY requirements.txt .**

This line copies the requirements.txt file from the host system into the container. The . at the end specifies the destination path, which is the current directory within the container (i.e., /app).

**RUN pip install -r requirements.txt**

This line uses the RUN instruction to execute a command within the container. In this case, it runs pip install -r requirements.txt to install all the Python packages listed in the requirements.txt file. This step ensures that the necessary dependencies are installed in the container.

**COPY . .**

This line copies all the files and directories from the current directory (the directory where the Dockerfile is located) on our host system to the /app directory in the container. This includes our application code (app.py) and any other files or directories in the project folder.

**EXPOSE 5000**

This line informs Docker that the container will listen on port 5000. While this line is not necessary for port mapping, it serves as a form of documentation to indicate which ports the container expects to use. It's good practice for clarity.

**CMD ["python", "app.py"]**

This line defines the default command to execute when the container is started. It runs the Flask application by executing python app.py. The Flask development server will start, and our application will be served on port 5000 as specified earlier.

Now lets learn how to run the application by using just two docker commands from your local machine. 

## Running the Application with Docker

### Building a Docker Image

To build a Docker image, you use the docker build command. 

```docker build -t flask-calculator .```

docker build -t flask-calculator .  Run this command where the Dockerfile is located. Our Dockerfile is located in the root directory cloudspace so that we use ( . )  

Now break down the code 

**docker build:** This is the Docker command for building an image.

**-t flask-calculator:** This part specifies the image name and tag. In this example, the image is named "flask-calculator," and it has no specific tag. The tag is used to version your images; if you don't specify one, it defaults to "latest."

**.:** The dot (.) at the end of the command tells Docker to use the current directory as the build context. It means that Docker will look in the current directory for a Dockerfile to use as the basis for building the image.

### Docker image used for :

A Docker image is a lightweight, standalone, executable package that includes everything needed to run a piece of software, including the code, runtime, system tools, system libraries, and settings. Images are used as the basis for creating Docker containers.

## Running a Docker Container

Once we have built our Docker image, we can create and run containers from it using the docker run command. 

```docker run -p 5000:80 -d flask-calculator```

Now break down the code 

**docker run:** This is the Docker command for creating and running a container.

**-p 5000:80:** This part maps port 5000 from our host machine to port 80 inside the container. It allows us to access our Flask application running in the container on port 5000.

**-d:** this defines the container will run in detached mode, which means it runs in the background.

**flask-calculator:** This is the name of the Docker image you want to use to create the container. The image name corresponds to the one you specified when building the image.

### Docker Container used for :
A Docker container is a runnable instance of a Docker image. It is an isolated environment where our application runs. Containers share the same OS kernel as the host system but are isolated from each other. They are ephemeral and can be started, stopped, and removed as needed.

## Access the application 
Now you will see the app is running from your local machine on port 5000.
Open your web browser and go to **localhost:5000** to access the Flask calculator web application within the Docker container.


