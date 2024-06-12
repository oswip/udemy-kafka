# Kafka with zookeeper, docker compose

Went the docker compose way instead of installing Kafka into any OS (Mac/Win/Lin)
Downloaded the docker-compose.yml in the with-zookeper folder from the following:
https://www.baeldung.com/ops/kafka-docker-setup

## Working with the containers

### Displaying specific containers

To display only the kafka container, filter by the exposed port:

```shell
docker ps --filter "expose=9092"
docker ps --filter "expose=9092" | grep kafka | awk '{print $1}'
```

### Using the kafka cli

To run the `kafka-topics` command:

```shell
docker exec $(docker ps --filter "expose=9092" | grep kafka | awk '{print $1}') kafka-topics
```
