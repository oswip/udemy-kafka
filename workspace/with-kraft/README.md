# Kafka without zookeeper (ie kraft), docker compose

Went the docker compose way instead of installing Kafka into any OS (Mac/Win/Lin)
Downloaded the docker-compose.yml in the with-kraft folder from the following:
https://codersee.com/how-to-set-up-kafka-without-zookeeper-using-docker-compose/

## Working with the containers

To run the `kafka-topics` command on the first kafka node (`kafka1`):

```shell
docker exec $(docker ps --filter "name=kafka1" | grep kafka | awk '{print $1}') kafka-topics
```

Or try kafka-topics, which uses the above command.

```shell
./kafka-topics --bootstrap-server kafka1:9092 --list
```

To go further, see the [howto](How-To-Set-Up-Kafka-Without-Zookeeper-using-Docker-Compose.md) extract from the link above.
