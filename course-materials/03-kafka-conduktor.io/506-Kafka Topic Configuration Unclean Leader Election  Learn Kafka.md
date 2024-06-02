When the leader for a partition is no longer available, one of the in-sync replicas (ISR) will be chosen as the new leader. This leader election is "clean" in the sense that it guarantees no loss of committed data - by definition, committed data exists on all ISRs.

But what to do when no ISR exists except for the leader that just became unavailable?

- Wait for an ISR to come back online. This is the default behavior, and this makes you run the risk of the topic becoming unavailable.

- Enable `unclean.leader.election.enable=true` and start producing to non-ISR partitions. We are going to lose all messages that were written to the old leader while that replica was out of sync and also cause some inconsistencies in consumers.

## Trade-off

**If we allow out-of-sync replicas to become leaders, we will have data loss and data inconsistencies.** If we don't allow them to become leaders, we face lower availability as we must wait for the original leader to become available before the partition is back online.

Overall this is a very dangerous setting and its implication must be understood fully before enabling it. For beginners, it is advisable to leave it to its default value of false.

However, this configuration can be enabled for some use cases including - metrics collection, log collection, and other cases where data loss is somewhat acceptable, at the trade-off of availability.

## Should you enable unclean leader election?

This in-depth blog can help you answer this question: [https://www.datadoghq.com/blog/kafka-at-datadog/#unclean-leader-elections-to-enable-or-not-to-enable](https://www.datadoghq.com/blog/kafka-at-datadog/#unclean-leader-elections-to-enable-or-not-to-enable)

## Setting “unclean.leader.election.enable=true” for a topic

Using the `kafka-configs` command it is possible:

`1` `kafka-configs.sh --bootstrap-server localhost:9092 --alter --entity-type topics --entity-name configured-topic --add-config unclean.leader.election.enable=true`

And to remove it:

`1` `kafka-configs.sh --bootstrap-server localhost:9092 --alter --entity-type topics --entity-name configured-topic --delete-config unclean.leader.election.enable`

###### Was this content helpful?[](mailto:support@conduktor.io?subject=Kafkademy Feedback <https://www.conduktor.io/kafkakafka-topic-configuration-unclean-leader-election>)