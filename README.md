# Docker, ELK Stack
Docker installation and running ELK stack on docker container.

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
