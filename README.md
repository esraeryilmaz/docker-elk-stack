# Docker, ELK Stack
Docker installation and running ELK stack on docker container.

## What is Docker?
Docker is a software platform that simplifies the process of building, running, managing and distributing applications. It does this by virtualizing the operating system of the computer on which it is installed and running.

## Docker Installation

### System requirements
Your machine must have WSL2 installed.

#### Quick WSL2 Installation
1. Start a command prompt as an administrator.
2. Type the following command to install the WSL2 on Windows 10.
```
$ wsl --install
```
if it didn't work, try:
```
$ wsl --install -d ubuntu
```
3. Restart your computer to finish the WSL2 installation on Windows 10.

#### Note : WSL2 consumes massive amounts of RAM. If you encounter this kind of problem, check this site : https://www.aleksandrhovhannisyan.com/blog/limiting-memory-usage-in-wsl-2/

### Docker Desktop Installation
After downloading Docker Desktop Installer.exe (https://desktop.docker.com/win/main/amd64/Docker%20Desktop%20Installer.exe) , run the following command on the command line.

```
$ start /w "" "Docker Desktop Installer.exe" install
```
Search the Docker Desktop in the search results , select and start.

## What is ELK Stack?
The ELK Stack is a collection of three open-source products — Elasticsearch, Logstash, and Kibana. ELK stack provides centralized logging in order to identify problems with servers or applications. It allows you to search all the logs in a single place.

- `E` stands for ElasticSearch : used for storing logs
- `L` stands for LogStash : used for both shipping as well as processing and storing logs
- `K` stands for Kibana : is a visualization tool (a web interface) which is hosted through Nginx or Apache

ELK Stack is designed to allow users to take data from any source, in any format, and to search, analyze, and visualize that data in real time.

## Installing ELK Stack on Docker
#### It includes Elasticsearch and Kibana.

1. Create a directory/folder with the name of ELK.
2. Open the ELK Directory in your IDE (VS Code in my case).
3. Create a file named docker-compose.yml in this directory.
4. Copy-paste the following commands : (it mentions some stuff about the dependencies we want to install, including Elastic Search and Kibana)
```
version: "3.7"
services:
  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.12.0
    container_name: elasticsearch
    restart: always
    environment:
      - xpack.security.enabled=false
      - discovery.type=single-node
    ulimits: 
      memlock:
        soft: -1 
        hard: -1
      nofile:
        soft: 65536
        hard: 65536
    cap_add: 
      - IPC_LOCK
    volumes:
      - elasticsearch-data-volume:/usr/share/elasticsearch/data
    ports:
      - "9200:9200"
  kibana:
    container_name: kibana
    image: docker.elastic.co/kibana/kibana:7.12.0
    restart: always
    environment:
      SERVER_NAME: kibana
      ELASTICSEARCH_HOSTS: http://elasticsearch:9200
    ports:
      - "5601:5601"
    depends_on:
      - elasticsearch
volumes: 
  elasticsearch-data-volume:
    driver: local
```

5. Save the File and run the following command under the same directory terminal.
```
$ docker-compose -f docker-compose.yml up -d
```

6. Once the download is completed, you can go either to the Docker Desktop and look up 2 new Docker containers created there(as seen in image) or you can type on your terminal :
```
$ docker container ls
```
![docker photo](https://github.com/esraeryilmaz/docker-elk-stack/blob/main/img/docker%20desktop.PNG)

7. You can run both the Elasticseacrh and Kibana Container, if it's not already been running from the Docker Desktop, run the button provided in front of the containers’ name.
You can run and stop it for further use too

8. Last step is to finally see the end results.

Go to http://localhost:5601/ (localhost server we provided for Kibana) and http://localhost:9200/ (Elasticsearch host server) to check if its working fine.

- You will get something like this on 9200:

![elasticsearch](https://github.com/esraeryilmaz/docker-elk-stack/blob/main/img/elasticsearch.PNG)

- And something like this with 5601:

![kibana](https://github.com/esraeryilmaz/docker-elk-stack/blob/main/img/kibana.PNG)

## ELK Stack Architecture

![elk stack]()

- `Logs` : Server logs that need to be analyzed are identified
- `Logstash` : Collect logs and events data. It even parses and transforms data
- `ElasticSearch` : The transformed data from Logstash is Store, Search, and indexed.
- `Kibana` : Kibana uses Elasticsearch DB to Explore, Visualize, and Share

### Elastic Search 
Elasticsearch is a real-time distributed and open source full-text search and analytics engine. It is accessible from RESTful web service interface and uses schema less JSON (JavaScript Object Notation) documents to store data.

![relational vs elastic]()

#### Key concepts or important terms used in Elasticsearch, which are ;  

1. `Node` : A node is an instance of Elasticsearch, which is used to store data. It creates when an Elasticsearch instance starts running.

2. `Cluster` : A cluster is a collection of nodes which together holds data and provides joined indexing and search capabilities.

3. `Document` : Documents are the things you’re searching for. They can be more than text – any structured JSON data works. Every document has a unique ID, and a type. 

4. `Field` : Columns in relational databases qualify as Fields in Elasticsearch. Each document has more than one field.

5. `Index` : Index is a group of different types of documents. It helps to perform search, update, and delete operation as well as indexing. In a relational database, an index is represented as a table, which means the index is analogous to a table in RDBMS. 

6. `Shard` : Every index can be split into several shards to be able to distribute data. The shard is the atomic part of an index, which can be distributed over the cluster if you want to add more nodes.

7. `Replicas` : Replicas are an additional copy of shards. They perform queries just like a shard. Elasticsearch enables the users to create replicas of their indexes and shards.

8. `Type` : In Elasticsearch, a type is defined for documents that have a common set of fields. It is a logical category of index whose semantics depends on users.

9. `Mapping` : In Elasticsearch, each index has a mapping associated with it, which is a schema-definition of the data that can be held by each individual document in an index. When the data is pushed to an index, the mapping is automatically added.


### Advantages of Elasticsearch
- High Performance
- Easily Scalable
- Document oriented (JSON)
- Open-source


