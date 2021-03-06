.. _docker:

Run Docker\* on Clear Linux\*
#############################

|CLOSIA| supports multiple containerization platforms, including a Docker\* 
solution. |CL| has many `unique features`_ including a minimal default 
installation which makes it compelling to use as a host for container 
workloads, management, and orchestration. 

This tutorial covers:

.. contents:: :local:
   :depth: 1

.. note::
   
   This tutorial focuses on the installaton of the Docker ecosystem. 
   If you want to use |CL| as a Docker container image, refer to the
   official `Clear Linux image on Docker Hub`_ 
   and `building a custom Clear Linux docker image`_ . 

Prerequisites
*************

* Basic understanding of Linux and Docker. 

* Successful completion: :ref:`bare-metal-install` 

* |CL| environment that has transparent network access to the Internet.
  If you are behind a HTTP proxy server, in a corporate setting for example,
  please refer to the `Docker proxy instructions`_ .

Install the containers-basic bundle
***********************************

Software in |CL| is offered in the form of `bundles`_ to provide a
complete function. The *containers-basic* provides all the required software 
packages to run Docker images as containers.  

#. First, install the *containers-basic* bundle by running this 
   :command:`swupd` command:

   .. code-block:: bash

      sudo swupd bundle-add containers-basic

#. Start the Docker daemon through systemd manager by running this command:

   .. code-block:: bash

      sudo systemctl start docker

   If you want Docker to start automatically on boot, enable the 
   systemd service by running this command:

   .. code-block:: bash

      sudo systemctl enable docker

#. Finally, verify :command:`docker` has been installed by running this  
   command and checking the version output for both *client* and *server*:

   .. code-block:: bash

      sudo docker version 

Congratulations! At this point, you have a working installation of Docker 
on |CL|. You are ready to start using container images on your system.

Integration with Clear Containers (optional)
********************************************

`Clear Containers`_, is an open source project aiming to increase security
of containers by using a hardware-backed virtual machine container runtime, 
instead of software namespace containers that are provided by the standard 
Docker *runc* runtime.

The Docker package from |CL| will automatically use the *cc-runtime* 
required for Clear Containers if it is available on your Clear Linux system. 

#. To take advantage of Clear Containers in |CL|, install the 
   *containers-virt* bundle by running this command:

   .. code-block:: bash

      sudo swupd bundle-add containers-virt

#. Restart the Docker daemon through systemd manager by running this command:

   .. code-block:: bash

      sudo systemctl restart docker

#. After restarting, the Docker daemon will seamlessly use Clear Containers
   to launch containers. You can see the runtime has changed to 
   :command:`cc-runtime` by running this command:

   .. code-block:: bash

      sudo docker info | grep Runtime

#. You should see the following output indicating the *cc-runtime* is the
   Default Runtime: 

   .. code-block:: console

      Runtimes: cc-runtime runc
      Default Runtime: cc-runtime

Congratulations! At this point, you have successfully replaced the default 
container runtime with Clear Containers. 

.. note:: 

   The proceeding sections of this tutorial are standard to Docker setup
   and configuration. If you are familiar with Docker basics, you do not 
   need to continue reading. The following sections are provided here for 
   sake of completeness.

Additional Docker configuration
*******************************

Perform additional Docker daemon configuration via a configuration file
typically located at :file:`/etc/docker/daemon.json`. |CL| features a 
`stateless system`_  so the configuration file :file:`daemon.json` does *NOT*
exist by default. 

#. Create the :file:`daemon.json` by running this command:

   .. code-block:: bash

      touch /etc/docker/daemon.json

   .. note:: 

      Refer to the `Docker documentation on daemon configuration`_ for the 
      full list of available configuration options and examples.

#. Once you've made any required changes, be sure to restart the 
   Docker daemon through systemd manager by running this command:

   .. code-block:: bash

      sudo systemctl restart docker

Pulling and Running an image from Docker Hub
********************************************

`Docker Hub`_ is a publically available container image repository which
comes preconfigured with Docker. In the example below we will pull and run 
an the official Docker image for nginx, an open source reverse proxy server. 

#. First, pull a container image from Docker Hub using the 
   :command:`docker pull` command. Download the latest nginx Docker 
   container image by running this command:

   .. code-block:: bash

      sudo docker pull nginx

#. Create and launch a new container using the :command:`docker run`
   command. Launch a nginx container by running this command:

   .. code-block:: bash

      sudo docker run --name test-nginx -d -p 8080:80 nginx

   .. note::
    
      Below is an explaination of switches used in the command above. For
      detailed :command:`docker run` switches and syntax, refer to the 
      `Docker Documentation`_ .

      * The :option:`--name` switch lets you provide a friendly name to
        target the container for future operations

      * The :option:`-d` switch launches the container in the background
        
      * The :option:`-p` switch allows the container's HTTP port (80) to be
        accessible from the Clear Linux host on port 8080

#. You can access the Welcome to Nginx! splash page running in the container
   by browsing to http://127.0.0.1:8080 or by running this :command:`curl` 
   command from your Clear Linux machine:

   .. code-block:: bash

      curl 127.0.0.1:8080

#. Finally, stop and delete the nginx container by running the 
   :command:`docker stop` and :command:`docker rm` commands.

   .. code-block:: bash

      sudo docker stop test-nginx 
      sudo docker rm test-nginx

Congratulations! At this point, you have successfully pulled a nginx 
container image from `Docker Hub`_ and ran an example container. 

Creating a Docker swarm cluster
*******************************

Clusters of Docker hosts are referred to as swarms.

The process in this tutorial can be repeated to install Docker on multiple
Clear Linux hosts with the intent to form a Docker swarm cluster.

The `Docker documentation on swarm key concepts`_ and 
`Docker documentation on creating a swarm`_ can be referenced 
for further instructions on setting up a swarm.

Also see:
---------
* `Docker Home`_
* `Docker Documentation`_
* `Docker Hub`_
* `Clear Containers`_ 

.. _`unique features`: https://clearlinux.org/features
.. _`Clear Linux image on Docker Hub`: https://hub.docker.com/_/clearlinux/ 
.. _`building a custom Clear Linux docker image`: https://clearlinux.org/documentation/clear-linux/guides/network/custom-clear-container
.. _`Docker proxy instructions`: https://docs.docker.com/config/daemon/systemd/#httphttps-proxy
.. _`bundles`: https://clearlinux.org/documentation/clear-linux/concepts/bundles-about#related-concepts 
.. _`stateless system`: https://clearlinux.org/features/stateless 
.. _`Docker documentation on daemon configuration`: https://docs.docker.com/engine/reference/commandline/dockerd/#daemon-configuration-file
.. _`Clear Containers`: https://github.com/clearcontainers
.. _`Docker Home`: https://www.docker.com/
.. _`Docker Documentation`: https://docs.docker.com/
.. _`Docker Hub`: https://hub.docker.com/
.. _`Docker documentation on swarm key concepts`: https://docs.docker.com/engine/swarm/key-concepts/
.. _`Docker documentation on creating a swarm`: https://docs.docker.com/engine/swarm/swarm-tutorial/create-swarm/
