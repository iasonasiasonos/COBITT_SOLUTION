# COBITT_SOLUTION

Prerequisites

To keep things simple all components will be run using Docker. Docker is a container technology which allows to different components isolated into their respective environments.

To install Docker on Windows follow the instructions here
To install Docker on Mac follow the instructions here
To install Docker on Linux follow the instructions here
Docker Compose is a tool for defining and running multi-container Docker applications. A YAML file is used configure the required services for the application. This means all container services can be brought up in a single command. Docker Compose is installed by default as part of Docker for Windows and Docker for Mac, however Linux users will need to follow the instructions found here

You can check your current Docker and Docker Compose versions using the following commands:

docker-compose -v
docker version

Start Up
Before you start you should ensure that you have obtained or built the necessary Docker images locally. Please clone the repository and create the necessary images by running the commands as shown:

git clone https://github.com/iasonasiasonos/COBITT_SOLUTION.git
cd COBITT_SOLUTION
docker compose up
