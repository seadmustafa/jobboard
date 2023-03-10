# Job Finder using React, Reactivesearch, Elasticsearch, Kibana and Docker

![React](https://img.shields.io/badge/react-%2320232a.svg?style=for-the-badge&logo=react&logoColor=%2361DAFB)
![ElasticSearch](https://img.shields.io/badge/-ElasticSearch-005571?style=for-the-badge&logo=elasticsearch)
![Kibana](https://img.shields.io/badge/Kibana-005571?style=for-the-badge&logo=Kibana&logoColor=white)
![Docker](https://img.shields.io/badge/docker-%230db7ed.svg?style=for-the-badge&logo=docker&logoColor=white)

## Environment Setup 


### ElasticSearch 
  1- Run the docker-compose.yml file.
  ```
  version: '3.7'
  services:
  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:8.6.2
    container_name: elasticsearch
    restart: always
    environment:
      - xpack.security.enabled=false
      - TAKE_FILE_OWNERSHIP=true
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
      - 9200:9200
      - 9300:9300 

  kibana:
    container_name: kibana
    image: docker.elastic.co/kibana/kibana:8.6.2
    restart: always
    environment:
      - ELASTICSEARCH_HOSTS=http://elasticsearch:9200
    ports:
      - 5601:5601
    depends_on:
      - elasticsearch

volumes:
  elasticsearch-data-volume:
    driver: local

  ```
  
  2- Open Kibana Create an index called jobboard.

```
PUT /jobboard/
{
  "settings": {
    "number_of_shards": 1
  },
  "mappings": {
    "properties": {
      "jobTitle": { "type": "text" },
      "company": { "type": "text" },
      "city": { "type": "text" },
      "description": { "type": "text" }
    }
  }
}

```
  3- Insert some data manually. Make sure that all the types are ```text``` not keyword. Copy and paste bulkdata.json into [Dev Console](http://localhost:5601/app/dev_tools#/console) . 
  Optional: Check if the records are inserted successfully [here](http://localhost:9200/jobboard/_search/?size=1000&pretty).
  
  4- Add the following lines into elasticsearch.yml . 
        * Create a local file ``elasticsearch.yml``. Copy the snippet below, save it and then copy it to the elasticsearch container.  
``` sh
sh-5.0$ cat config/elasticsearch.yml
 cluster.name: "docker-cluster"
 network.host: 0.0.0.0
 http.cors.enabled : true
 http.cors.allow-origin: "*"
 http.cors.allow-methods: OPTIONS, HEAD, GET, POST, PUT, DELETE
 http.cors.allow-headers: X-Requested-With,X-Auth-Token,Content-Type,Content-Length
 http.cors.allow-credentials: truesh-5.0$ ^C
 ```
 
 5- Check the changes by using ```cat config/elasticsearch.yml``` and then restart the container.
 
### React  
6- Install dependencies by using ```npm i``` command.
7- Run the project by using ```npm start``` command.



 
