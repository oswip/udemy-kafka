# Kafka cli basics

## Getting Started

Enter the `workspace/with-kraft` folder

To add this folder to $PATH, add the following to the .bashrc, ONLY ONCE:

``` shell
echo "export PATH=$PATH:$PWD" >> ~/.bashrc
```

Start the 3-broker kafka cluster:

``` shell
docker compose up
```

## Topics

List kafka topics:

``` shell
./kafka-topics --list
```

Create a topic:

``` shell
kafka-topics --topic first_topic --create
kafka-topics --list
```

Create more topics:

``` shell
kafka-topics --topic second_topic --create
kafka-topics --topic third_topic --create
kafka-topics --list
```

Describe a topic

``` shell
kafka-topics --topic first_topic --describe
```

Delete a topic

``` shell
kafka-topics --topic second_topic --delete
kafka-topics --list
```

At this point, it's best to remove any topics to preprare for the next sections

## Producer CLI

Create a topic with 1 partition

``` shell
kafka-topics --topic first_topic --create --partitions 1
```

Use producer command to publish a message

``` shell
kafka-console-producer --topic first_topic "Hello World
My name is Conduktor
I love Kafka"
```

Use producer command to publish a message with certain properties

``` shell
kafka-console-producer --topic first_topic --producer-property acks=all "
some message that is acked
just for fun
fun learning"
```

A kafka broker can be configured to have default partitions, but this is not recommended for production.