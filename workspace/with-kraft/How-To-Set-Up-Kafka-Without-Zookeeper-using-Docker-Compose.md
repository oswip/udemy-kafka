## 1\. Introduction

In this last of the 3 articles about setting up Apache Kafka, I would like to show you how to set up Apache Kafka **Without Zookeeper** using Docker Compose.

After reading it, you will know precisely:

- what does the Docker Compose configuration look like to set up **3 Kafka Brokers**,
- what **environment variables** we have to set,
- and what **workaround** we have to do in order to make it run.

If after finishing it, you will still be eager to broaden your knowledge, then you can find the previous two articles right here:

- [How To Deploy Multiple Kafka Brokers With Docker Compose?](https://codersee.com/how-to-deploy-multiple-kafka-brokers-with-docker-compose/)
- [How To Set Up Apache Kafka With Docker?](https://codersee.com/how-to-set-up-apache-kafka-with-docker/)

> In my articles, I focus on the practice and getting things done.
> 
> However, if you would like to get a strong understading of DevOps concepts, which are more and more in demand nowadays, then I highly recommend to check out KodeKloud courses, like this [Docker](https://shrsl.com/46wvo) and [Kubernetes](https://shareasale.com/r.cfm?b=2183704&u=3507867&m=132199&urllink=&afftrack=) learning paths.

## 2\. Apache Kafka Without Zookeeper

Before we dive into the practice part, let’s take a second to understand the motivation behind the Zookeeper removal (I’ve already written about Zookeeper responsibilities in the first part, so I’ll skip it here).

To put it simply, the motivation behind it can be summarized with these points:

- simplifying the deployment and configuration process (Zookeeper is a separate system, with its own syntax, configs, and deployment patterns),
- scalability improvement,
- enabling support for more partitions.

The whole change has been introduced for the first time as a [KIP-500](https://cwiki.apache.org/confluence/display/KAFKA/KIP-500%3A+Replace+ZooKeeper+with+a+Self-Managed+Metadata+Quorum) (Kafka Improvement Proposal) and has been offered as an early access version with the **2.8.0 release**.

> If you would like to understand deeply the reasons and arguments behind the Zookeeper removal, I highly encourage you to visit the KIP-500 ticket page linked above.

## 3\. Kafka Without Zookeeper Docker Compose File

With that being said, let’s take a look at the **docker-compose.yml** file:

```yaml
services:
  kafka1:
    image: confluentinc/cp-kafka:7.2.1
    container_name: kafka1
    environment:
      KAFKA_NODE_ID: 1
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: CONTROLLER:PLAINTEXT,PLAINTEXT:PLAINTEXT
      KAFKA_LISTENERS: PLAINTEXT://kafka1:9092,CONTROLLER://kafka1:9093
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://kafka1:9092
      KAFKA_CONTROLLER_LISTENER_NAMES: 'CONTROLLER'
      KAFKA_CONTROLLER_QUORUM_VOTERS: '1@kafka1:9093,2@kafka2:9093,3@kafka3:9093'
      KAFKA_PROCESS_ROLES: 'broker,controller'
    volumes:
      - ./run_workaround.sh:/tmp/run_workaround.sh
    command: "bash -c '/tmp/run_workaround.sh && /etc/confluent/docker/run'"
  kafka2:
    image: confluentinc/cp-kafka:7.2.1
    container_name: kafka2
    environment:
      KAFKA_NODE_ID: 2
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: CONTROLLER:PLAINTEXT,PLAINTEXT:PLAINTEXT
      KAFKA_LISTENERS: PLAINTEXT://kafka2:9092,CONTROLLER://kafka2:9093
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://kafka2:9092
      KAFKA_CONTROLLER_LISTENER_NAMES: 'CONTROLLER'
      KAFKA_CONTROLLER_QUORUM_VOTERS: '1@kafka1:9093,2@kafka2:9093,3@kafka3:9093'
      KAFKA_PROCESS_ROLES: 'broker,controller'
    volumes:
      - ./run_workaround.sh:/tmp/run_workaround.sh
    command: "bash -c '/tmp/run_workaround.sh && /etc/confluent/docker/run'"
  kafka3:
    image: confluentinc/cp-kafka:7.2.1
    container_name: kafka3
    environment:
      KAFKA_NODE_ID: 3
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: CONTROLLER:PLAINTEXT,PLAINTEXT:PLAINTEXT
      KAFKA_LISTENERS: PLAINTEXT://kafka3:9092,CONTROLLER://kafka3:9093
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://kafka3:9092
      KAFKA_CONTROLLER_LISTENER_NAMES: 'CONTROLLER'
      KAFKA_CONTROLLER_QUORUM_VOTERS: '1@kafka1:9093,2@kafka2:9093,3@kafka3:9093'
      KAFKA_PROCESS_ROLES: 'broker,controller'
    volumes:
      - ./run_workaround.sh:/tmp/run_workaround.sh
    command: "bash -c '/tmp/run_workaround.sh && /etc/confluent/docker/run'"
```



Don’t worry if you feel overwhelmed at this point, I will walk you step by step through the configuration values in the following paragraphs.

Nevertheless, don’t run the

docker compose up

command yet, we will need one more file to run it 🙂

## 4\. Add Necessary Workarounds

As the next step, we need to run a few commands when containers start.

In my example, I will call this file **run\_workaround.sh**. And although the name is not important, it has to match values specified when mounting volumes and running the command from within the containers:

```shell
sed -i '/KAFKA\_ZOOKEEPER\_CONNECT/d' /etc/confluent/docker/configure

sed -i 's/cub zk-ready/echo ignore zk-ready/' /etc/confluent/docker/ensure

echo "kafka-storage format --ignore-formatted -t NqnEdODVKkiLTfJvqd1uqQ== -c /etc/kafka/kafka.properties" >> /etc/confluent/docker/ensure
```



As we can see, the above script contains exactly 3 commands.

The first one is responsible for turning off the check for the `KAFKA\_ZOOKEEPER\_CONNECT` parameter. Without this line, the error will be thrown: C_ommand \[/usr/local/bin/dub ensure KAFKA\_ZOOKEEPER\_CONNECT\] FAILED !_

The second one ignores the cub zk-ready. Without it, everything will end up with another exception: _zk-ready: error: too few arguments_.

And finally, the last line is a KRaft required step, which formats the storage directory with a new cluster identifier. Please keep in mind that the specified UUID value (`NqnEdODVKkiLTfJvqd1uqQ==` in my case) has to be **16 bytes** of a **base64-encoded UUID**.

## 5\. Docker Compose Instructions

With all of the above being said, we can get back to the **docker-compose.yml** file.

### 5.1. Volumes and Commands

Although I will skip the things like the _services_, _images_, or _container_ names (I’ve covered them in the previous articles), let’s take a look at these 3 lines:

```shell
volumes:
  - ./run_workaround.sh:/tmp/run_workaround.sh
command: "bash -c '/tmp/run_workaround.sh && /etc/confluent/docker/run'"
```



_Volumes_ are the preferred mechanism for persisting data **generated by and used** by Docker containers. With the above setting, we inform Docker that we want the **run\_workaround.sh** file to be mounted inside the **tmp** directory inside the container with the same name. Of course, we could change the name of this file inside, but then, the change should also be reflected in the command.

When it comes to the _command_, it simply **overrides the default command**. With the above config, we instruct Docker to run our container with our custom script.

## 6\. Apache Kafka Brokers Environment Variables

Finally, let’s go through all the environments we had to set in order to make everything work.

### 6.1. KAFKA\_NODE\_ID Environment Variable

As the name suggest, the `KAFKA_NODE_ID` variable is used to specify a unique identifier of our node in the cluster.

### 6.2. Kafka Listeners and Protocol Mapping

Nextly, let’s see the settings responsible for appropriate hosts and ports mapping:

- **KAFKA\_LISTENER\_SECURITY\_PROTOCOL\_MAP** – with this one, we simply define the key-value pairs of listeners’ names with security protocols used with them
- **KAFKA\_LISTENERS** – with this variable, we define all exposed listeners
- **KAFKA\_ADVERTISED\_LISTENERS** – this one, on the other hand, contains all listeners used by clients

It’s worth mentioning here that when working with Docker Compose, the container name becomes a hostname- like _kafka1_, _kafka2_, and _kafka3_ in our examples.

### 6.3. KAFKA\_ADVERTISED\_LISTENERS Setting

As the next step, let’s see the `KAFKA_ADVERTISED_LISTENER`.

This one, simply allows us to specify a list of coma-separated listeners’ names used by the controller. It’s necessary when running in a KRaft mode.

Additionally, none of the values specified here can be put inside the advertised listeners covered in the previous paragraph. Otherwise, the following error will be thrown:

> The advertised.listeners config must not contain KRaft controller listeners from controller.listener.names when process.roles contains the broker role because Kafka clients that send requests via advertised listeners do not send requests to KRaft controllers — they only send requests to KRaft brokers.

### 6.4. KAFKA\_CONTROLLER\_QUORUM\_VOTERS

As the next one comes the

KAFKA\_CONTROLLER\_QUORUM\_VOTERS

environment variable. And this one allows us to specify the set of voters in our node. When it comes to the format, values have to be put in the following order: `{id}@{host}:{port}`.

So, in our case, we do specify `9093`

of our nodes, because this is the port exposed as a controller.

### 6.5. KAFKA\_PROCESS\_ROLES Variable

And the last one- the `KAFKA_PROCESS_ROLES` variable lets us specify the role of the particular broker. The value can be set to either **broker**, **controller**, or **both of them** (just like above).

## 7\. Testing

And finally, we can proceed to the part where we will verify if everything is working, as expected. In my example, I will show you how to use Console Producer/Consumer, but feel free to connect with anything you like.

Firstly, let’s start our services with the `docker compose up`:

```shell
The broker has been unfenced. Transitioning from RECOVERY to RUNNING.
```



After some time, we should see the above for each of our containers.

Additionally, we can validate with a `docker ps` command:

```shell
CONTAINER ID IMAGE COMMAND CREATED STATUS PORTS NAMES
dc3966ba58a4 confluentinc/cp-kafka:7.2.1 "bash -c '/tmp/run\_w…" About a minute ago Up About a minute 9092/tcp kafka3
10242d9d1e6a confluentinc/cp-kafka:7.2.1 "bash -c '/tmp/run\_w…" About a minute ago Up About a minute 9092/tcp kafka2
c404c866cde6 confluentinc/cp-kafka:7.2.1 "bash -c '/tmp/run\_w…" About a minute ago Up About a minute 9092/tcp kafka1
```

As we can clearly see, every container is up and running. Moreover, the desired `9092` port is exposed for each of them.

When it comes to the testing strategy, I would like to connect to each broker separately and perform a different action:

- firstly, connect to the `kafka1` and create a new topic called`exampleTopic`

- secondly, to `kafka2` and produce some messages

- and finally, read all of the messages from within the `kafka3`



To do so, let’s start with the following:

```shell
docker exec -it kafka1 /bin/bash
kafka-topics --bootstrap-server kafka1:9092 --create --topic exampleTopic

# Output
Created topic exampleTopic.

# To exit the container, please specify exit
```



As we can see, the `exampleTopic` was created successfully.

So as the next thing, let’s produce some messages from `kafka2` broker:

```shell
docker exec -it kafka2 /bin/bash
kafka-console-producer --bootstrap-server kafka2:9092 --topic exampleTopic

>codersee
>rules

# To exit the producer, please hit Ctrl + D. Then to exit the container please type 'exit'.
```



No errors have been thrown, which let us assume that everything is good so far.

Nevertheless, to fully make sure, let’s consume these messages:

```shell
docker exec -it kafka3 /bin/bash
kafka-console-consumer --bootstrap-server kafka3:9092 --topic exampleTopic --from-beginning

# Output:
codersee
rules

# To exit the consumer, please hit Ctrl + C. As the output, we should see:
^CProcessed a total of 2 messages
# And again, to exit the container please type 'exit'.
```



As we can see, everything worked as expected and our brokers are correctly communicating within the cluster.

## 

## 8\. Kafka Without Zookeeper Using Docker Compose Summary

And that’s all for today’s article on how to **set up Kafka Without Zookeeper using Docker Compose**.

I really hope that you enjoyed this and my previous articles in an Apache Kafka series and I will be forever thankful if you would like to share your feedback with me in the comments section below (positive and negative, as well).

Take care and have a great week! 🙂