# Monitoring with Prometheus

 In the intricate tapestry of modern software architecture, the ability to monitor and analyze the performance and health metrics of applications is pivotal. This project embarks on a journey to demystify the realm of monitoring by introducing Prometheus, a powerful open-source monitoring and alerting toolkit, and Grafana, a versatile platform for visualizing time-series data.

 Monitoring applications in real-time is indispensable for identifying bottlenecks, detecting anomalies, and ensuring optimal performance. Prometheus, with its robust data model and flexible querying language, has emerged as a cornerstone in the domain of monitoring, providing developers and operators with the tools needed to gain insights into the intricate workings of their systems.

 This project is designed to elucidate the fundamental concepts of Prometheus and Grafana by guiding participants through the process of setting up a monitoring infrastructure for their applications. By integrating Prometheus to collect and store time-series data and Grafana to create intuitive dashboards, developers can gain actionable insights into the health and performance of their systems.

 Throughout this project, we will delve into the intricacies of Prometheus, exploring its capabilities for metric collection, querying, and alerting. Grafana will then complement this monitoring setup by providing a visually appealing and customizable interface to represent and analyze the collected metrics.

 By the culmination of this project, participants will have a solid understanding of how to implement Prometheus for monitoring and Grafana for visualization, empowering them to make informed decisions based on real-time data. Let's embark on this exploration of monitoring and visualization to unlock the potential of Prometheus and Grafana in enhancing the observability of your applications.

### **Running Prometheus with Docker-Compose**

**Introduction**

This guide will walk you through the process of running Prometheus, a powerful monitoring and alerting tool, using Docker. By containerizing Prometheus, you can easily deploy and 
manage it across different environments.

### **Prerequisites**
Docker installed on your machine.Docker | installation guide [Click Here](https://docs.docker.com/engine/install/ubuntu/)

### **Getting Started**
first open terminal and create a directory and navigate to that directory by using this command 
```
mkdir my-prometheus-project
cd my-prometheus-project
```
Now create a **Dockerfile** by using this command:

```nano Dockerfile```

In that **Dockerfile** implement this following codes:
```
# Use a base image with a minimal installation of Alpine Linux
FROM alpine:latest

# Create a directory for Prometheus data
RUN mkdir /prometheus

# Download and install Prometheus
ADD https://github.com/prometheus/prometheus/releases/download/v2.30.2/prometheus-2.30.2.linux-amd64.tar.gz /prometheus/prometheus.tar.gz
RUN tar xvf /prometheus/prometheus.tar.gz -C /prometheus/ --strip-components=1 && rm /prometheus/prometheus.tar.gz

# Copy the Prometheus configuration file into the image
COPY prometheus.yml /prometheus/prometheus.yml

# Expose the Prometheus web UI and API
EXPOSE 9090

# Set the working directory to /prometheus
WORKDIR /prometheus

# Command to run when the container starts
CMD ["/prometheus/prometheus", "--config.file=/prometheus/prometheus.yml"]
```
Now press ctrl+x and then press 'y' to save the file 

Now create a another file named **prometheus.yml** by running this command 

```touch prometheus.yml```

### **Building the Docker Image**

**Build the Docker Image:**
Open a terminal in the same directory as your Dockerfile and run the following commands:

```docker build -t my-prometheus .```

Now we are gonna create a **docker-compose.yml** file to run prometheus.

Now create a **docker-compose.yml** by using this command:

```nano docker-compose.yml```

now implement the following codes in that file:

```
version: '3'

services:
  prometheus:
    image: my-prometheus:latest
    container_name: prometheus
    ports:
      - "9090:9090"
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml

```
Now press ctrl+x and then press 'y' to save the file

**Running Prometheus Container**

Once the Docker image is built, you can run a Prometheus container using the following command:

```docker-compose up -d```

This maps port 9090 in the Prometheus container to port 9090 on your host machine.

**Accessing Prometheus Web Interface**

Open your web browser and navigate to **localhost:9090** to access the Prometheus web interface.



**Conclusion**

You have successfully set up Prometheus using Docker. This containerized deployment allows for easy distribution and management of your monitoring infrastructure.

