# COBITT

<h1>Prerequisites</h1>

<p>
To keep things simple all components will be run using <a href="https://www.docker.com" rel="nofollow">Docker</a>. Docker is a container technology which allows to different components isolated into their respective environments.
</p>
<p>
To install Docker on Windows follow the instructions <a href="https://docs.docker.com/docker-for-windows/" rel="nofollow">here</a>
To install Docker on Mac follow the instructions <a href="https://docs.docker.com/docker-for-mac/" rel="nofollow">here</a>
To install Docker on Linux follow the instructions <a href="https://docs.docker.com/install/" rel="nofollow">here</a>
Docker Compose is a tool for defining and running multi-container Docker applications. A <a href="https://raw.githubusercontent.com/Fiware/tutorials.Entity-Relationships/master/docker-compose.yml" rel="nofollow">YAML file</a> is used configure the required services for the application. This means all container services can be brought up in a single command. Docker Compose is installed by default as part of Docker for Windows and Docker for Mac, however Linux users will need to follow the instructions found <a href="https://docs.docker.com/compose/install/" rel="nofollow">here</a>
</p>
<p>
You can check your current Docker and Docker Compose versions using the following commands:
</p>
<pre>
docker-compose -v
docker version
</pre>

<h1>Start Up</h1>
<p>
Before you start you should ensure that you have obtained or built the necessary Docker images locally. Please clone the repository and create the necessary images by running the commands as shown:
</p>
<pre>
git clone https://github.com/iasonasiasonos/COBITT_SOLUTION.git
cd COBITT_SOLUTION
docker compose up
</pre>
