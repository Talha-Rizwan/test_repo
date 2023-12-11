.. _docker_tutorial:

Docker Tutorial
===============

Overview
--------

Docker is a platform for developing, shipping, and running applications in containers. Containers allow developers to package an application with all of its dependencies, including libraries and runtime, and ship it as a single package. This ensures that the application runs consistently across different environments, making it easy to deploy and scale.

Docker Images (Layered Architecture)
------------------------------------

Docker uses a layered architecture for images, providing efficiency and flexibility. Each layer in a Docker image represents a set of file changes or instructions in the Dockerfile. Layers are cached, and if there are no changes in a particular layer, Docker reuses the cached layer, speeding up the image build process.

The layered architecture allows for incremental builds and facilitates sharing common layers between different images, reducing the overall storage space and improving effeciency.

for example: redis image needs following images to complete itself::
   
   $ docker pull redis
   Using default tag: latest
   latest: Pulling from library/redis
   1f7ce2fa46ab: Downloading [=======>                                           ]  4.483MB/29.15MB
   3c6368585bf1: Download complete 
   3911d271d7d8: Download complete 
   ac88aa9d4021: Downloading [====================>                              ]    8.6MB/20.83MB
   127cd75a68a2: Download complete 
   4f4fb700ef54: Download complete 
   f3993c1104fc: Download complete 



Difference in Docker Images and Containers
------------------------------------------

**Docker Images:**

- Docker images are the building blocks for containers.
- They are lightweight, standalone, and executable packages that include everything needed to run a piece of software, including the code, runtime, libraries, and system tools.
- Images are defined by a set of layers, each representing a specific instruction in the Dockerfile.

**Docker Containers:**

- Containers are instances of Docker images that run in isolation on the host system.
- They encapsulate the application and its dependencies, ensuring consistent behavior across different environments.
- Containers are portable, enabling seamless deployment across various environments without modification.

Dockerfile Example
------------------

This below example Dockerfile is for a Flask application and includes common Dockerfile instructions::

   FROM ubuntu:20.04                                        # Use an official base image
   
   WORKDIR /app                                             # Set the working directory inside the container
   
   COPY . /app                                              # Copy the local directory's contents into the container at /app
   
   RUN apt-get update && \                                  # Install necessary dependencies
       apt-get install -y \
       python3 \
       python3-pip \
       && rm -rf /var/lib/apt/lists/*
   
   RUN pip3 install -r requirements.txt                     # Install Python dependencies
   
   EXPOSE 5000                                              # Expose port 5000 to the outside world
   
   ENV FLASK_APP=app.py                                     # Define environment variable
   
   CMD ["flask", "run", "--host=0.0.0.0"]                   # Command to run on container start




Docker Container Debugging
--------------------------

**- Viewing Container Logs**

To check the logs of a container::

   docker logs <container-id>

Adding the -f option allows you to follow the log output in real-time.

**- Accessing a Container Shell**

For interactive debugging, accessing a shell inside a running container can be invaluable. Use::

   docker exec -it <container-id> /bin/bash

Replace ``/bin/bash`` with the appropriate shell for your image (e.g., ``/bin/sh``).

**- Inspecting Container Details**

To get detailed information about a container::

   docker inspect <container-id>

This will provide information in all aspects including configuration, networking, and environment variables

**- Attaching to a Running Container**

To interact with the main process of a running container::

    docker attach <container-id>

This allows you to see the output of the container's main process and send input to it.

**- Copying Files to/from a Container**

Files can be moved to/from your local machine and a Docker container::

   # Copy from local to container
   docker cp local_file.txt container_id:/path/in/container/

   # Copy from container to local
   docker cp container_id:/path/in/container/local_file.txt .

This is useful for moving configuration files, scripts, or debugging tools into or out of a container.

**- Monitoring Container Resource Usage**

To monitor the resource usage of a running container::

   docker stats <container-id>

This command provides real-time statistics on CPU usage, memory usage, network I/O, and block I/O.


Docker Networking
-----------------

Docker provides a flexible and powerful networking model that allows containers to communicate with each other and with the outside world.

Docker containers can be connected through various types of networks. To learn about all of them, visit `docker docs <https://docs.docker.com/network/drivers/>`_.

**Creating a Bridge Network**

The default network driver is the **bridge** network, which allows containers on the same host to communicate with each other using their container names. Each container connected to the bridge network gets its own IP address.

To create a custom bridge network::

   docker network create my-bridge-network

This creates a new bridge network named `my-bridge-network`.

**Connecting Containers to a Network**

When starting a container, you can specify the network it should connect to:

   docker run --network=my-bridge-network -d --name=container1 my-image

Here, `my-bridge-network` is the name of the network, and `container1` is the name of the running container.

**Inspecting Networks**

To view details about a Docker network, use::

   docker network inspect <network-name>

This command provides information such as network ID, subnet, gateway, and connected containers.

Docker Volumes
--------------

Docker volumes provide a flexible and persistent way to manage data in containers. Volumes allow data to be shared and stored independently of the container lifecycle, ensuring that data persists even when containers are stopped or removed. Volumes can be shared among multiple containers, facilitating data collaboration.

**Creating Volumes**

Docker volumes can be created::

   docker volume create <volume-name>

**Attaching Volumes to Containers**

To use a volume, you need to attach it to a container during the container creation or when starting an existing container::

   docker run -v <volume-name>:/path/in/container -d <image>

Here, `/path/in/container` is the path where the volume is mounted inside the container.

**Inspecting Volumes**

To view details about a Docker volume::

   docker volume inspect <volume-name>

This command provides information about the volume, such as its name, driver, mount point, and labels.

For more detailed information, refer to the official `Docker documentation <https://docs.docker.com/storage/volumes/>`_ on volumes.


Docker Compose
--------------

Managing all containers, networks, volumes can sometimes get a little overwhelmed but don't worry, that's where docker-compese comes into play. Using Docker Compose simplifies the process of orchestrating multi-container applications, and it is particularly useful for development, testing, and staging environments.

Docker Compose is a tool for defining and running multi-container Docker applications. It allows you to describe all services, networks, and volumes in a single `docker-compose.yml` file, serves as a blueprint for defining the entire application stack, making it easy to manage and deploy complex applications.


**Key Concepts:**

- *Services:* Services represent the containers that make up the application.
- *Networking:* Defines how containers communicate with each other.
- *Volume Mounts:* Persists data generated by and used by containers.
- *Environment Variables and Configuration:* Docker Compose allows you to set environment variables for services, making it simple to configure different environments (e.g., development, testing, production) without modifying the application code.
- *Scaling:* Docker Compose makes it easy to scale services horizontally by specifying the number of replicas for a service. This is useful for load balancing and improving application performance.

**Docker Compose Common Commands:**

Navigate to the directory containing your ``docker-compose.yml`` file and run::

   docker-compose up

This command creates and starts the containers defined in the ``docker-compose.yml`` file. To run it in detached mode, use ``docker-compose up -d``

Inversely, to stop and remove the containers::

   docker-compose down

After changes to your Dockerfile or related build context::

   docker-compose build

This will (re)build the services.

To scale a service to the specified number of replicas::

   docker-compose up --scale <service_name>=<number_of_replicas>

Scaling is useful in scenarios where you want to distribute incoming requests or workloads across multiple instances of a service. 

To see and validate the composed configuration, use::

   docker-compose config

Note: These commands can only execute in the same directory as ``docker-compose.yml``

**Example:**
Let’s dive deep into an example to learn how docker-compose actually saves the day.

To clearify the picture and understand the struture of docker-compose, take a trivial example to create and start two docker containers i-e mongodb and mongo-express and connect them via a single docker network.

1. Let's first do it without using docker-compose.

- First create a docker-network for containers to communicate using just the container name::

   Docker network create mongo-net

- Start mongodb container::
   
   docker run -d \                                     (running in the detach mode)
   -p 27017:27017 \                                    (specify the port)
   -e MONGO_INITDB_ROOT_USERNAME=admin \               (specify environment variable)
   -e MONGO_INITDB_ROOT_PASSWORD=password \            (specify environment variable)
   –net mongo-network \					                   (network for container)
   –name mongodb \	           					          (container name)
   mongo 							                         (image name)

- Start mongo-express container::

   docker run -d \                                     (running in the detach mode)
   -p 8081:8081 \                                      (specify the port)
   -e ME_CONFIG_MONGODB_ADMINUSERNAME=admin \          (specify environment variable)
   -e ME_CONFIG_MONGODB_ADMINPASSWORD=password \       (specify environment variable)
   -e ME_CONFIG_MONGODB_SERVER=mongodb\                (specify environment variable (mongodb container) )
   –net mongo-network \                                (network for container)
   –name mongo-express \                               (container name)
   mongo-express                                       (image name)


2. Now, let’s try to achieve the same outcome with a ``docker-compose.yml`` file


- Structure of docker-compose::

   Version: ‘<latest-version>’
   Services:					                              (list of containers)
   mongodb:				                                    (container name)
   		image:mongo			                              (image need to create container)
   		ports:
   		   -27017:27017		                           (port host:container)
   		environment:				                        (environment variables)
                  -MONGO_INITDB_ROOT_USERNAME=admin
                  -MONGO_INITDB_ROOT_PASSWORD=password
   
      mongo-express:					                        (container name)
   		image:mongo-express			                     (image need to create container)
   		ports:
   		   -8081:8081				                        (port host:container)
   		environment:					                     (environment variables)
                  -ME_CONFIG_MONGODB_ADMINUSERNAME=admin
                  -ME_CONFIG_MONGODB_ADMINPASSWORD=password
                  -ME_CONFIG_MONGODB_SERVER=mongodb


You would have noticed that the network configuration is not there in the docker-compose. Docker compose takes care of creating a common network for containers, so we don’t have to create the network manually.




Common Troubleshooting
----------------------

**1. Docker Daemon Not Running:**

   - **Issue:** Docker commands fail because the Docker daemon is not running.
   - **Solution:**
     - Start the Docker daemon using::
       
         sudo systemctl start docker   # On systems using systemd
     or::

         sudo service docker start    # On systems using init.d

**2. Insufficient Disk Space:**

   - **Issue:** Running out of disk space on the host machine.
   - **Solution:**
     - Clean up unused Docker resources using::
         docker system prune -a
       
**3. Port Already in Use:**

   - **Issue:** Unable to start a container because the specified port is already in use.
   - **Solution:**
     - Choose a different port, or stop the process using the occupied port.

**4. Image Not Found Locally:**

   - **Issue:** Docker cannot find the specified image locally.
   - **Solution:**
     - Pull the image from the registry using::
         docker pull image_name:tag


Checkout this `cheatsheet <https://quickref.me/docker.html/>`_. for a quick reach of common docker commands.
