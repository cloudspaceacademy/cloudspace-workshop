# CI/CD Beginner
In the ever-evolving landscape of software development, the seamless integration of efficient and reliable Continuous Integration/Continuous Deployment (CI/CD) pipelines is imperative for delivering high-quality applications with speed and consistency. This project embarks on the journey of demystifying the CI/CD process using GitHub Actions and orchestrating the deployment of a Dockerized application to both development and production environments within the Amazon Web Services (AWS) ecosystem.

The core objective of this project is to provide a clear understanding of the fundamental concepts surrounding CI/CD, containerization with Docker, and deployment in AWS environments. By leveraging GitHub Actions, a powerful and flexible automation tool integrated into the GitHub platform, developers gain the ability to automate workflows, conduct automated testing, and seamlessly deploy applications, all within the same version control environment.

The chosen technology stack revolves around Docker, offering containerization to encapsulate the application and its dependencies, ensuring consistency across various environments. AWS serves as the deployment platform, with a focus on providing scalable and resilient infrastructure to host Dockerized applications.

Throughout this project, we will explore the step-by-step process of building a CI/CD pipeline, containerizing an application, and deploying it to both a development and a production environment. By the end, developers will not only have a functional CI/CD pipeline but will also grasp the principles and practices essential for streamlining software development workflows in a modern, cloud-native context. Let's embark on this journey to unlock the potential of CI/CD, Docker, and AWS for a more efficient and robust software development lifecycle.

ow let's elaborate how we write that pipeline.yml to provide Continous Integration of our application.
## Dockerfile 

```
name: Docker Image CI

on:
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]

jobs:

  build:

    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v3
    - name: Build the Docker image
      run: docker build -t flask-calculator cloudspace/

```
Now just break down all the block and Elaborate why they use for 

**name: Docker Image CI**

This line specifies the name of the pipeline..


```
on:
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]
```
This block specifies the trigger of this pipeline.
Trigger Events:

Push: The workflow is triggered when there is a push to the "main" branch.
Pull Request: The workflow is triggered when there is a pull request targeting the "main" branch

```
jobs:

  build:

    runs-on: ubuntu-latest
```

Runs on: Ubuntu latest means this build job will runs on a ubuntu machine
```
 steps:
    - uses: actions/checkout@v3
    - name: Build the Docker image
      run: docker build -t flask-calculator cloudspace/

```
Steps: Builds the docker image using regular docker command


