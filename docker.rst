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

To view details about a Docker network, you can use the `docker network inspect` command::

   docker network inspect my-bridge-network

This command provides information such as network ID, subnet, gateway, and connected containers.



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
