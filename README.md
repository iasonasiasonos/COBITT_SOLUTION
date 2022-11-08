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

<h1>Installation</h1>
<p>
Before you start you should ensure that you have obtained or built the necessary Docker images locally. Please clone the repository and create the necessary images by running the commands as shown:
</p>
<pre>
git clone https://github.com/iasonasiasonos/COBITT_SOLUTION.git
cd COBITT_SOLUTION
docker compose up
</pre>

<h1>
Orion Context Broker Configuration
</h1>

<p>
    The Orion Context Broker is an implementation of the Publish/Subscribe Context Broker GE, providing an NGSI interface.
</p>

<pre>
orion:
    labels:
      org.fiware: 'tutorial'
    image: fiware/orion:latest
    hostname: orion
    container_name: fiware-orion1
    depends_on:
      - mongo-db
    networks:
      - default
    expose:
      - "1026"
    ports:
      - "1026:1026"
    command: -dbhost mongo-db -logLevel DEBUG
    healthcheck:
      test: curl --fail -s http://orion:1026/version || exit 1
      interval: 5s
</pre>


<h1>
  Mongo DB Configuration
</h1>

<p>
    MongoDB is required to run either in the same host where Orion Context Broker is to be installed or in a different host accessible through the network. 
</p>

<pre>
mongo-db:
    labels:
      org.fiware: 'tutorial'
    image: mongo:latest
    hostname: mongo-db
    container_name: db-mongo1
    expose:
      - "27017"
    ports:
      - "27017:27017"
    networks:
      - default
    volumes:
      - mongo-db:/data
    healthcheck:
      test: |
        host=`hostname --ip-address || echo '127.0.0.1'`; 
        mongo --quiet $host/test --eval 'quit(db.runCommand({ ping: 1 }).ok ? 0 : 2)' && echo 0 || echo 1
      interval: 5s
</pre>


<h1>
IoT Agent Configuration
</h1>

<p>
    The iot-agent container relies on the precence of the Orion Context Broker and uses a MongoDB database to hold device information such as device URLs and Keys. 
</p>

<pre>
iot-agent:
    image: fiware/iotagent-json:latest
    hostname: iot-agent
    container_name: fiware-iot-agent1
    depends_on:
      - mongo-db
    networks:
      - default
    expose:
      - "4041"
      - "7896"
    ports:
      - "4041:4041"
      - "7896:7896"
    environment:
      - IOTA_CB_HOST=orion
      - IOTA_CB_PORT=1026
      - IOTA_NORTH_PORT=4041
      - IOTA_REGISTRY_TYPE=mongodb
      - IOTA_LOG_LEVEL=DEBUG
      - IOTA_DEFAULT_EXPRESSION_LANGUAGE=jexl
      - IOTA_TIMESTAMP=true
      - IOTA_CB_NGSI_VERSION=v2
      - IOTA_AUTOCAST=true
      - IOTA_MONGO_HOST=mongo-db
      - IOTA_MONGO_PORT=27017
      - IOTA_MONGO_DB=iotagentjson
      - IOTA_HTTP_PORT=7896
      - IOTA_PROVIDER_URL=http://iot-agent:4041
      - IOTA_DEFAULT_RESOURCE=/iot/json
    healthcheck:
      interval: 5s
</pre>

<h2>
  IOT Agent - ROS2
</h2>

<p>
    Find the new IOT Agent for ROS2 <a href="https://github.com/iasonasiasonos/iot_agent_ros2">here.</a>
</p>

<h1>
  SQL Server DB Configuration
</h1>

<p>
    This database is used to hold data for the ordering system of COBITT.
</p>

<pre>
sql-server-db:
    container_name: sql-cobit-db
    image: iasonasiasonos/dih2_cobit_db:latest
    ports:
      - "1433:1433"
    environment:
      SA_PASSWORD: "2Secure*Password2"
      ACCEPT_EULA: "Y"
</pre>


<h1>
  COBITT Web App Configuration
</h1>

<p>
    Provides a user friendly web application to allow users creating IOT Devices using the IOT Agent and Orion Context Broker as well as to communicate with these devices. Additionally it holds an ordering system to keep track the orders if there are succesfull or failed.
</p>

<pre>
 cobit-webapp:
    image: iasonasiasonos/dih2_webapp:latest
    container_name: fiware-cobit-webapp1
    hostname: iot-sensors
    depends_on:
      - orion
      - iot-agent
    networks:
      default:
        aliases:
          - tutorial
          - context-provider
    expose:
      - "3000"
      - "3001"
    ports:
      - "3000:3000"
      - "3001:3001"
    environment:
      - "DEBUG=tutorial:*"
      - "WEB_APP_PORT=3000"
      - "IOTA_HTTP_HOST=iot-agent"
      - "IOTA_HTTP_PORT=7896"
      - "IOTA_DEFAULT_RESOURCE=/iot/json"
      - "DUMMY_DEVICES_PORT=3001"
      - "DUMMY_DEVICES_TRANSPORT=HTTP"
      - "DUMMY_DEVICES_API_KEY=4jggokgpepnvsb2uv4s40d59ov"
      - "DUMMY_DEVICES_PAYLOAD=JSON"
      - "CONTEXT_BROKER=http://orion:1026/v2"
</pre>

<h1>
  MySQL DB Configuration
</h1>

<p>
    Database to hold persisting context data.
</p>

<pre>
mysql-db:
      restart: always
      image: mysql:latest
      hostname: mysql-db
      container_name: db-mysql1
      expose:
          - "3306"
      ports:
          - "3306:3306"
      networks:
          - default
      environment:
          - "MYSQL_ROOT_PASSWORD=123"
          - "MYSQL_ROOT_HOST=%"
</pre>

<h1>
  Cygnus Configuration
</h1>

<p>
   Cygnus is a connector in charge of persisting context data sources into other third-party databases and storage systems, creating a historical view of the context. 
</p>
    
<pre>
cygnus:
      image: fiware/cygnus-ngsi:latest
      hostname: cygnus
      container_name: fiware-cygnus1
      networks:
          - default
      depends_on:
          - mysql-db
      expose:
          - "5080"
      ports:
          - "5050:5050"
          - "5080:5080"
      environment:
          - "CYGNUS_MYSQL_HOST=mysql-db"
          - "CYGNUS_MYSQL_PORT=3306"
          - "CYGNUS_MYSQL_USER=root"
          - "CYGNUS_MYSQL_PASS=123"
          - "CYGNUS_LOG_LEVEL=DEBUG"
          - "CYGNUS_SERVICE_PORT=5050"
          - "CYGNUS_API_PORT=5080"
</pre>

<h1>
  Grafana Configuration
</h1>

<p>
    Grafana allows you to query, visualize, alert on and understand your metrics no matter where they are stored.
</p>

<pre>
grafana:
    image: grafana/grafana
    container_name: grafana1
    depends_on:
        - mysql-db
    ports:
        - "3003:3000"
    environment:
        - GF_INSTALL_PLUGINS=https://github.com/orchestracities/grafana-map-plugin/archive/master.zip;grafana-map-plugin,grafana-clock-panel,grafana-worldmap-panel
    networks:
      - default
</pre>
