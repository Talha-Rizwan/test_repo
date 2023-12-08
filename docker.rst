.. _docker_tutorial:

Docker Tutorial
===============

Overview
--------

Docker is a platform for developing, shipping, and running applications in containers. Containers allow developers to package an application with all of its dependencies, including libraries and runtime, and ship it as a single package. This ensures that the application runs consistently across different environments, making it easy to deploy and scale.

Docker Images (Layered Architecture)
------------------------------------

Docker uses a layered architecture for images, providing efficiency and flexibility. Each layer in a Docker image represents a set of file changes or instructions in the Dockerfile. Layers are cached, and if there are no changes in a particular layer, Docker reuses the cached layer, speeding up the image build process.

The layered architecture allows for incremental builds and facilitates sharing common layers between different images, reducing the overall storage space. This layered approach is fundamental to Docker's efficiency and is a key factor in its popularity.

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

To check the logs of a running or stopped container, you can use::

   docker logs <container-id>

Adding the -f option allows you to follow the log output in real-time.

**- Accessing a Container Shell**

For interactive debugging, accessing a shell inside a running container can be invaluable. Use::

   docker exec -it <container-id> /bin/bash

Replace ``/bin/bash`` with the appropriate shell for your image (e.g., ``/bin/sh``).

**- Inspecting Container Details**

To get detailed information about a container, including its configuration, networking, and environment variables, use::

   docker inspect <container-id>

**- Attaching to a Running Container**

If you need to interact with the main process of a running container, you can attach to it using the ``docker attach`` command:

    docker attach <container-id>

This allows you to see the output of the container's main process and send input to it. Press Ctrl + C to detach.

**- Copying Files to/from a Container**

To copy files between your local machine and a Docker container, use::

   # Copy from local to container
   docker cp local_file.txt container_id:/path/in/container/

   # Copy from container to local
   docker cp container_id:/path/in/container/local_file.txt .

This is useful for moving configuration files, scripts, or debugging tools into or out of a container.

**- Monitoring Container Resource Usage**

To monitor the resource usage of a running container, you can use the ``docker stats`` command:

   docker stats <container-id>

This command provides real-time statistics on CPU usage, memory usage, network I/O, and block I/O.


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
  
