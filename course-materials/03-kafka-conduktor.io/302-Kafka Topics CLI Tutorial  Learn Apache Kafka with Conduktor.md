Kafka Topics CLI, i.e., `kafka-topics` is used to create, delete, describe, or change a topic in Kafka. Make sure you have [started Kafka](https://www.conduktor.io/kafka/starting-kafka/) beforehand.

CLI Extensions

Use CLI commands with appropriate extensions for your platform, e.g., `kafka-topics.bat` for windows, `kafka-topics.sh` for Linux and Mac

___

## How to create a Kafka topic?

To create a Kafka topic, we need to provide the mandatory parameters:

-   If Kafka v2.2+, use the Kafka hostname and port e.g., `localhost:9092`
    
-   If older version of Kafka, use the Zookeeper URL and port e.g. `localhost:2181`
    
-   Provide the mandatory parameters: topic name, number of partitions and replication factor.
    
-   Use the `kafka-topics.sh` CLI with the `--create` option
    

Important recommendation: if you use Kafka v2.2+

If you have a recent version of Kafka, we strongly recommend you to use the command with the `--bootstrap-server` option because the Zookeeper option is now deprecated and is removed as part of Kafka v3.

### Example

Creating the Kafka topic `first_topic` with 3 partitions and a replication factor of 1 when my Kafka broker is running at `localhost:9092`

**Kafka v2.2+**:

`1` `kafka-topics.sh --bootstrap-server localhost:9092 --topic first_topic --create --partitions 3 --replication-factor 1`

**Kafka v2.1 or less:**

`1` `kafka-topics.sh --zookeeper localhost:2181 --topic first_topic --create --partitions 3 --replication-factor 1`

### Command Output

`1 2` `WARNING: Due to limitations in metric names, topics with a period ('.') or underscore ('_') could collide. To avoid issues it is best to use either, but not both. Created topic first_topic.`

The WARNING is saying that you shouldn't mix underscores and periods in topic name

### Gotchas

Here are the common mistakes and caveats with the `kafka-topics.sh --create` command:

-   **You cannot specify a replication factor greater than the number of brokers you have**
    
-   You can specify as many partitions as you want, 3 is a good number to get started with in dev
    
-   If you see the documentation after running a command, this means your command is wrong. Scroll up to see details about the error message
    
-   There are no default for partitions and replication factor and you must specify those explicitly
    
-   The topic name must contain only ASCII alphanumerics, '.', '\_' and '-'
    

`--config`

You can set [topic-level configurations](https://kafka.apache.org/documentation/#topicconfigs) for example `--config max.message.bytes=64000`

`--disable-rack-aware`

Disable rack aware replica assignment (not recommended, set only if you know what you're doing)

___

## How to list Kafka Topics?

To list Kafka topics, we need to provide the mandatory parameters:

-   If Kafka v2.2+, use the Kafka hostname and port e.g., `localhost:9092`
    
-   If older version of Kafka, use the Zookeeper URL and port e.g. `localhost:2181`
    
-   Use the `kafka-topics.sh` CLI with the `--list` option
    

Important recommendation: if you use Kafka v2.2+

If you have a recent version of Kafka, we strongly recommend you to use the command with the `--bootstrap-server` option because the Zookeeper option is now deprecated and is removed as part of Kafka v3.

### Example

Listing topics when my Kafka broker is running at `localhost:9092`

**Kafka v2.2+**:

`1` `kafka-topics.sh --bootstrap-server localhost:9092 --list`

**Kafka v2.1 or less:**

`1` `kafka-topics.sh --zookeeper localhost:2181 --list`

### Command Output

`1 2 3 4` `__consumer_offsets first_topic second_topic test`

### Gotchas

Here are the common mistakes and caveats with the `kafka-topics.sh --list` command:

-   This command lists internal topics that you won't have created (such as `__consumer_offsets`). Do not try to delete these topics. Use `--exclude-internal` if you want to hide these topics
    
-   If you see the documentation after running a command, this means your command is wrong. Scroll up to see details about the error message
    

`--exclude-internal`

Exclude internal topics when running list or describe command (they're listed by default)

**To filter topics based on replication status:**

`--at-min-isr-partitions`

If set when describing topics, only show partitions whose isr count is equal to the configured minimum.

`--unavailable-partitions`

Only show partitions whose leader is not available.

`--under-min-isr-partitions`

Only show partitions whose isr count is less than the configured minimum.

`--under-replicated-partitions`

Only show under replicated partitions

___

## How to describe a Kafka topic?

To describe a Kafka topic and get partition details, we need to provide the mandatory parameters

1.  If Kafka v2.2+, use the Kafka hostname and port e.g., `localhost:9092`
    
2.  If older version of Kafka, use the Zookeeper URL and port e.g. `localhost:2181`
    
3.  Use the `kafka-topics.sh` CLI with the `--describe` option
    

Important recommendation: if you use Kafka v2.2+

If you have a recent version of Kafka, we strongly recommend you to use the command with the `--bootstrap-server` option because the Zookeeper option is now deprecated and is removed as part of Kafka v3.

### Example

Listing topics when my Kafka broker is running at `localhost:9092`

**Kafka v2.2+**:

`1` `kafka-topics.sh --bootstrap-server localhost:9092 --describe --topic first_topic`

**Kafka v2.1 or less:**

`1` `kafka-topics.sh --zookeeper localhost:2181 --describe --topic first_topic`

You can specify a comma delimited list of topics to describe more than one at a time.

### Command Output

`1 2 3 4` `Topic: first_topic TopicId: fN9mf1UDSmiCdKIDFyMEIQ PartitionCount: 3 ReplicationFactor: 1 Configs: cleanup.policy=delete Topic: first_topic Partition: 0 Leader: 1 Replicas: 1 Isr: 1 Topic: first_topic Partition: 1 Leader: 1 Replicas: 1 Isr: 1 Topic: first_topic Partition: 2 Leader: 1 Replicas: 1 Isr: 1`

`Leader: 1` means that for partition 0, the broker with the ID `1` is the leader.

`Replicas: 1` means that for partition 0, the broker with the ID `1` is a replica.

`Isr: 1` means that for partition 0, the broker with the ID `1` is an in-sync replica.

**In case the command is running against a Kafka cluster, you may see**

`1 2 3 4 5` `Topic: third_topic TopicId: 84yqCErzTG27J4wv44dkPQ PartitionCount: 4 ReplicationFactor: 3 Configs: cleanup.policy=delete Topic: third_topic Partition: 0 Leader: 2 Replicas: 2,3,1 Isr: 2,3,1 Topic: third_topic Partition: 1 Leader: 3 Replicas: 3,1,2 Isr: 3,1,2 Topic: third_topic Partition: 2 Leader: 1 Replicas: 1,2,3 Isr: 1,2,3 Topic: third_topic Partition: 3 Leader: 2 Replicas: 2,1,3 Isr: 2,1,3`

`Leader: 2` means that for partition `0`, the broker with the ID `2` is the leader.

`Replicas: 2,3,1` means that for partition `0`, the brokers with the ID 2, 3 and 1 are replicas.

`Isr: 2,3,1` means that for partition `0`, the brokers with the ID 2, 3 and 1 are replicas in-sync replica.

### Gotchas

Here are the common mistakes and caveats with the `kafka-topics.sh --describe` command:

-   It's easy to get partition ID and broker ID confused. Look at the Command Output section above to make sense of what the numbers mean in the command output
    
-   If you see the documentation after running a command, this means your command is wrong. Scroll up to see details about the error message
    
-   You can specify a comma delimited list to describe a list of topics
    
-   If you don't specify a `--topic` option, it will describe all topics
    

**To describe all topics (usually combined with the options describe after)**

Do not set the `--topic` argument and you will describe all Kafka topics

**To filter partitions based on replication status:**

`--at-min-isr-partitions`

If set when describing topics, only show partitions whose ISR count is equal to the configured minimum.

`--unavailable-partitions`

Only show partitions whose leader is not available

`--under-min-isr-partitions`

Only show partitions whose ISR count is less than the configured minimum.

`--under-replicated-partitions`

Only show under replicated partitions

___

## How to increase the number of partitions of a Kafka topic?

To increase the number of partitions in a Kafka topic, we need to provide the mandatory parameters.

1.  If Kafka v2.2+, use the Kafka hostname and port e.g., `localhost:9092`
    
2.  If older version of Kafka, use the Zookeeper URL and port e.g. `localhost:2181`
    
3.  Provide the mandatory parameters: topic name and number of partitions
    
4.  Use the `kafka-topics.sh` CLI with the `--alter` option
    

Danger: Increasing Partitions

Increasing the number of partitions in a Kafka topic a DANGEROUS OPERATION if your applications are relying on key-based ordering. In that case, create a new topic and copy all data there instead to have keys properly re-distributed.

### Example

Altering the Kafka topic `first_topic` to have 5 partitions when my Kafka broker is running at `localhost:9092`

**Kafka v2.2+**:

`1` `kafka-topics.sh --bootstrap-server localhost:9092 --alter --topic first_topic --partitions 5`

**Kafka v2.1 or less:**

`1` `kafka-topics.sh --zookeeper localhost:2181 --alter --topic first_topic --partitions 5`

### Command Output

The command does not have any output, although you can verify afterwards with a `--describe` command.

`1 2 3 4 5 6 7` `$ kafka-topics.sh --bootstrap-server localhost:9092 --describe --topic first_topic Topic: first_topic TopicId: fN9mf1UDSmiCdKIDFyMEIQ PartitionCount: 5 ReplicationFactor: 1 Configs: cleanup.policy=delete Topic: first_topic Partition: 0 Leader: 1 Replicas: 1 Isr: 1 Topic: first_topic Partition: 1 Leader: 1 Replicas: 1 Isr: 1 Topic: first_topic Partition: 2 Leader: 1 Replicas: 1 Isr: 1 Topic: first_topic Partition: 3 Leader: 1 Replicas: 1 Isr: 1 Topic: first_topic Partition: 4 Leader: 2 Replicas: 2 Isr: 2`

### Gotchas

Here are the common mistakes and caveats with the `kafka-topics.sh --alter` command:

-   **This command is NOT RECOMMENDED to run when your consumers are relying on key-based ordering as changing the number of partitions changes the key-hashing technique**
    
-   You can only add partitions, not remove partitions
    

___

## How to Delete a Kafka Topic?

To delete a Kafka topic, we need to provide the mandatory parameters.

-   If Kafka v2.2+, use the Kafka hostname and port e.g., `localhost:9092`
    
-   If older version of Kafka, use the Zookeeper URL and port e.g. `localhost:2181`
    
-   Use the `kafka-topics.sh` CLI with the `--delete` option
    
-   Ensure that the Kafka brokers allow for topic deletion `delete.topic.enable=true` (default)
    

Important recommendation: if you use Kafka v2.2+

If you have a recent version of Kafka, we strongly recommend you to use the command with the `--bootstrap-server` option because the Zookeeper option is now deprecated and is removed as part of Kafka v3.

### Example

Deleting the topic `first_topic` when my Kafka broker is running at `localhost:9092`

**Kafka v2.2+**:

`1` `kafka-topics.sh --bootstrap-server localhost:9092 --delete --topic first_topic`

**Kafka v2.1 or less:**

`1` `kafka-topics.sh --zookeeper localhost:2181 --delete --topic first_topic`

Multiple Topics

You can specify a comma delimited list of topics to delete more than one topic at a time

### Command Output

You can verify the outcome of the command with a `kafka-topics.sh --describe` command.

It make take some time (depending on the topic size) to see the effect of the command.

### Gotchas

Here are the common mistakes and caveats with the `kafka-topics.sh --delete` command:

-   In case topic deletion is not enabled (see the `delete.topic.enable` broker setting) then the topics will be "marked for deletion" but will not be deleted
    
-   Deleting a topic in Kafka may take some time and this is why the `kafka-topics` command returns an empty output even before the topic is deleted (only the command is sent)
    
-   You can specify a comma delimited list to delete a list of topics
    

Windows Users (if not running on WSL2): do NOT delete topics

Windows has a long-standing bug ([KAFKA-1194](https://issues.apache.org/jira/browse/KAFKA-1194)) that makes Kafka crash if you delete topics. The only way to recover from this error is to manually delete the topic folders in `data/kafka`.

**If you are running Kafka on WSL2, you can safely delete topics**