# Load Balancing with Docker, Nginx, and Apache: A README Guide

This README guide will walk you through the process of setting up a simple load-balancing scenario using Docker, Nginx, and Apache. By the end of this tutorial, you will be able to distribute incoming HTTP traffic between two Apache web servers using Nginx as the load balancer.

This project is the result of asking OpenAI's [GPT-4](https://openai.com/research/gpt-4): "What would be a component of a distributed system and give me example of an exercise to practice with?"



## Prerequisites

- [Docker](https://www.docker.com) installed
- [Nginx](https://www.nginx.com) installed

## Installation Steps

### Step 1: Install Docker

If you haven't already installed Docker, you can download and install it from the [official Docker website](https://www.docker.com/products/docker-desktop).

### Step 2: Create a Docker Network

Open your terminal and run the following command to create a Docker network:

```zsh
docker network create mynetwork
```

### Step 3: Run Apache Containers

To run two Apache containers named `apache1` and `apache2`, execute the following commands:

```zsh
docker run -d --name apache1 --network mynetwork -p 8081:80 -v ./apache1.html:/usr/local/apache2/htdocs/index.html httpd
docker run -d --name apache2 --network mynetwork -p 8082:80 -v ./apache2.html:/usr/local/apache2/htdocs/index.html httpd
```

### Step 4: Run Nginx Load Balancer

Assuming you have an Nginx configuration file named `load-balancer.conf`, run the following command to start the Nginx container:

```zsh
docker run -d --network mynetwork -p 8083:80 -v ./load-balancer.conf:/etc/nginx/nginx.conf:ro nginx
```

### Step 5: Test Load Balancer

Run the following `curl` commands to test the load balancing:

```zsh
curl localhost:8083
curl localhost:8083
curl localhost:8083
curl localhost:8083
curl localhost:8083
curl localhost:8083
```

You should see output similar to the following:

```zsh
❯ curl localhost:8083
🎉 Congrats! You've successfully connected to Apache Server 1! 🎉
❯ curl localhost:8083
🎉 Congrats! You've successfully connected to Apache Server 2! 🎉
❯ curl localhost:8083
🎉 Congrats! You've successfully connected to Apache Server 1! 🎉
❯ curl localhost:8083
🎉 Congrats! You've successfully connected to Apache Server 2! 🎉
```

### Step 6: Verifying Load Balancer Functionality Through Logs

After executing the `curl` commands, you should check the logs for both Apache servers (`apache1` and `apache2`) to verify that the load balancer is functioning correctly.

```zsh
docker logs apache1
docker logs apache2
```

#### What to Look For in Logs

- **200 OK**: This status code indicates that the request was successful.
  
- **304 Not Modified**: This status code indicates that the resource has not been modified since the last request.
  
- **Load Distribution**: You should see a roughly equal number of log entries on both Apache servers, which means the load balancer is effectively distributing incoming requests.

By closely observing these logs, you can ensure that the load balancer is effectively distributing incoming traffic between your Apache servers.

### Step 7: Cleanup

Once you're done, you can stop and remove the containers:

```zsh
docker stop apache1
docker stop apache2
docker stop <CONTAINER_ID>  # use `docker ps` to find the container ID
```

---

That's it! You have successfully set up a simple load-balanced environment using Docker, Nginx, and Apache. Feel free to expand on this basic setup to better fit your specific use cases.
