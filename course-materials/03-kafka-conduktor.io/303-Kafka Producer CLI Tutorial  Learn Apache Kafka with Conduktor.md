The Kafka console producer CLI, kafka-console-producer is used to read data from standard input and publish it to Kafka. Make sure you have [started Kafka](https://www.conduktor.io/kafka/starting-kafka/) beforehand.

CLI Extensions

Use CLI commands with appropriate extensions for your platform, e.g., `kafka-console-producer.bat` for windows, `kafka-console-producer.sh` for Linux and Mac

## How to Produce a Message into a Kafka Topic using the CLI?

To produce to a Kafka topic, we need to provide the mandatory parameters:

-   Find your Kafka hostname and port e.g., `localhost:9092`
    
-   If Kafka v2.5+, use the `--bootstrap-server` option
    
-   If older version of Kafka, use the `--broker-list` option
    
-   Provide the mandatory parameters: topic name
    
-   Use the `kafka-console-producer.sh` CLI as outlined below
    

### Example

Make sure the topic first\_topic is already created with 3 partitions and a replication factor of 1:

`1` `kafka-topics.sh --bootstrap-server localhost:9092 --topic first_topic --create --partitions 3 --replication-factor 1`

Start producing to the topic:

**Kafka v2.5+**:

`1` `kafka-console-producer.sh --bootstrap-server localhost:9092 --topic first_topic`

**Kafka v2.4 or less:**

`1` `kafka-console-producer.sh --broker-list localhost:9092 --topic first_topic`

To exit the Kafka console producer, use the keyboard combination `Ctrl+C`.

### Command Output

After the producer is opened, you should see a `>` sign. Then any line of text you write afterwards will be sent to the Kafka topic (when pressing `Enter`)

`1 2 3 4 5` `$ kafka-console-producer --bootstrap-server localhost:9092 --topic first_topic >Hello World >My name is Conduktor >I love Kafka >^C (<- Ctrl + C is used to exit the producer)`

### Gotchas

Here are the common mistakes and caveats with the `kafka-console-producer.sh` command:

-   Messages are sent with the `null` key by default (see below for more options)
    
-   **If the topic does not exist, it can be auto-created by Kafka:**
    

A topic with the name provided should exist. If you specify a topic that does not exist yet, a new topic with the name provided will be created with the **default number of partitions and replication factor.**

These are controlled by the broker-side settings (in your `config/server.properties` file), with the following defaults:

`1 2 3` `auto.create.topics.enable=true num.partitions=1 default.replication.factor=1`

`--compression-codec`

To enable message compression, default `gzip`, possible values `'none'`, `'gzip', 'snappy', 'lz4', or 'zstd'`

`--producer-property`

To pass in any producer property, such as the `acks=all` setting

`--request-required-acks`

An alternative to set the `acks` setting directly

___

## How to produce messages from a file with the Kafka Console Producer CLI?

Example file `topic-input.txt` (make sure each message is on a new line)

`1 2` `Hello World My name is Stephane`

Produce messages to the topic from the file (see the end of the command)

`1` `kafka-console-producer.sh --bootstrap-server localhost:9092 --topic first_topic < topic-input.txt`

___

## How to produce messages with key in the Kafka Console Producer CLI?

By default messages sent to a Kafka topic will result in messages with `null` keys.

We must use the properties `parse.key` and `key.separator` to send the key alongside messages.

In this example, the separator between the key and the value is: `:`

`1` `kafka-console-producer.sh --bootstrap-server localhost:9092 --topic first_topic --property parse.key=true --property key.separator=:`

Example input:

`1 2` `>example key:example value >name:Stephane`

Do not forget to always include your key/value separator otherwise you will get an exception.