# Storm Kafka Cantos

This is an open repo of all the knowledge about Storm, Kafka, and the meeting of the two that I have gathered from working with these frameworks.

Currently super unorganized- just shotgunning the knowledge out for now.

# Kafka

#### Partitions

- Partitions is your degree of parallelism. The number of partitions should be 1:1 with the number of consumer threads (spread across however many hosts) on the consumer side.

- Ideal world is 1:1 partitions per Storm worker, which would mean one thread per worker.

- Oversubscribing workers (having greater than 1 partition per Storm worker) may cause workers to idle more. If processing lag is getting too far behind the number of workers (and/or threads? unsure) should be increased.

# Storm

#### Bolt Latency and Complete Latency

- Spout Complete Latency: the time a tuple is emitted until Spout.ack() is called.

- Bolt Execution Latency: the time it take to run Bolt.execute().

- Bolt Processing Latency: the time Bolt.execute() is called until the bolt acks the given input tuple.

If you do not ack each incoming input tuple in Bolt.execute immediately (which is absolutely ok), processing latency can be much higher than execution latency.

Furthermore, the processing latencies must not add up to the complete latency because tuple can stay in internal input/output buffers. This add additional time, until the last ack is done, thus increasing complete latency. Furthermore, the ackers need to process all incoming acks and notify the Spout about fully processed tuples. This also adds to the complete latency.

To the problem could be to large internal buffers between operators. This could be resolve by either increasing the dop(degree of parallelism) or by setting parameter TOPOLOGY_MAX_SPOUT_PENDING -- this limits the number of tuple within the topology. Thus, if too many tuples are in-flight the spout stops to emit tuples until it received acks. Therefore, tuples does not queue up in internal buffers and complete latency goes down. If this does not help, you might need to increase the number of ackers. If the acks are not processed fast enough, the acks could buffer up, increasing the complete latency, too.

Source: https://stackoverflow.com/questions/33558613/storm-huge-discrepancy-between-bolt-latency-and-total-latency

#### Bottlenecks

- Bottleneck is going to be datastore queries or purest requests.

#### Backpressure

Source: https://jobs.one2team.com/apache-storms/

#### Message Buffers

(Placeholder for information about Storm's message buffers- what is a Message? What is a Message Buffer? Why do I care?)
