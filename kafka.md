**Data Pipeline Infrastructure**: This is a short demo on how to produce and consume messages in kafka using python.

# Work with Zookeeper

1. Start Zookeeper Server

>docker run -d -p 2181:2181 -p 2888:2888 -p 3888:3888 --name zookeeper confluent/zookeeper

2. Get Zookeeper CLI

>wget http://apache.mirrors.ionfish.org/zookeeper/zookeeper-3.4.8/zookeeper-3.4.8.tar.gz

> tar xvf zookeeper-3.4.8.tar.gz

> mv zookeeper-3.4.8 zookeeper
>
> ./zkCli.sh -server localhost:2181

# Work with Kafka

1. Start Kafka Server
> docker run -d -p 9092:9092 -e KAFKA_ADVERTISED_HOST_NAME=localhost -e KAFKA_ADVERTISED_PORT=9092 --name kafka --link zookeeper:zookeeper confluent/kafka
2. Get Kafka CLI
> wget http://apache.mirrors.ionfish.org/kafka/0.10.0.1/kafka_2.11-0.10.0.1.tgz
> tar xvf kafka_2.11-0.10.0.1.tgz
> mv kafka_2.11-0.10.0.1 kafka
> rm kafka_2.11-0.10.0.1.tgz
3. Create Kafka Topic
  Note: Start zookeeper and start kafka container as described above
>./kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic bigdata
>./kafka-topics.sh --list --zookeeper localhost:2181
4. Produce Messages
>./kafka-console-producer.sh --broker-list localhost:9092 --topic bigdata

The screen will just hang there, you can type messages into the window to send messages
5. Consume Messages
> ./kafka-console-consumer.sh --zookeeper localhost:2181 --topic bigdata
> ./kafka-console-consumer.sh --zookeeper localhost:2181 --topic bigdata --from-beginning
6. Look Into Kafka Broker
> docker exec -it kafka bash
> cd /var/lib/kafka
> ls

# Errors
1. Create topic error
> Bin:bin bin$ ./kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic bigdata
> [2018-01-17 15:11:42,316] WARN Session 0x0 for server null, unexpected error, closing socket connection and attempting reconnect (org.apache.zookeeper.ClientCnxn)
> java.net.ConnectException: Connection refused
	at sun.nio.ch.SocketChannelImpl.checkConnect(Native Method)
	at sun.nio.ch.SocketChannelImpl.finishConnect(SocketChannelImpl.java:717)
	at org.apache.zookeeper.ClientCnxnSocketNIO.doTransport(ClientCnxnSocketNIO.java:361)
	at org.apache.zookeeper.ClientCnxn$SendThread.run(ClientCnxn.java:1141)
> Created topic "bigdata".

However, I am able to list this topic.

> Bin:bin bin$ ./kafka-topics.sh --list --zookeeper localhost:2181
>
> bigdata

2. Consume messsages
  Bin:bin bin$ ./kafka-console-consumer.sh --zookeeper localhost:2181 --topic bigdata
  Using the ConsoleConsumer with old consumer is deprecated and will be removed in a future major release. Consider using the new consumer by passing [bootstrap-server] instead of [zookeeper].
  [2018-01-17 15:13:45,813] WARN Session 0x0 for server null, unexpected error, closing socket connection and attempting reconnect (org.apache.zookeeper.ClientCnxn)
  java.net.ConnectException: Connection refused
  at sun.nio.ch.SocketChannelImpl.checkConnect(Native Method)
  at sun.nio.ch.SocketChannelImpl.finishConnect(SocketChannelImpl.java:717)
  at org.apache.zookeeper.ClientCnxnSocketNIO.doTransport(ClientCnxnSocketNIO.java:361)
  at org.apache.zookeeper.ClientCnxn$SendThread.run(ClientCnxn.java:1141)
  [2018-01-17 15:14:46,614] WARN [ConsumerFetcherThread-console-consumer-208_Bin.local-1516230820802-ef4624df-0-0]: Error in fetch to broker 0, request Name: FetchRequest; Version: 3; CorrelationId: 0; ClientId: console-consumer-208; ReplicaId: -1; MaxWait: 100 ms; MinBytes: 1 bytes; MaxBytes:2147483647 bytes; RequestInfo: (bigdata-0,PartitionFetchInfo(1,1048576)) (kafka.consumer.ConsumerFetcherThread)
  java.net.SocketTimeoutException
  at sun.nio.ch.SocketAdaptor$SocketInputStream.read(SocketAdaptor.java:211)
  at sun.nio.ch.ChannelInputStream.read(ChannelInputStream.java:103)
  at java.nio.channels.Channels$ReadableByteChannelImpl.read(Channels.java:385)
  at org.apache.kafka.common.network.NetworkReceive.readFromReadableChannel(NetworkReceive.java:122)
  at kafka.network.BlockingChannel.readCompletely(BlockingChannel.scala:131)
  at kafka.network.BlockingChannel.receive(BlockingChannel.scala:122)
  at kafka.consumer.SimpleConsumer.liftedTree1$1(SimpleConsumer.scala:102)
  at kafka.consumer.SimpleConsumer.kafka$consumer$SimpleConsumer$$sendRequest(SimpleConsumer.scala:86)
  at kafka.consumer.SimpleConsumer$$anonfun$fetch$1$$anonfun$apply$mcV$sp$1.apply$mcV$sp(SimpleConsumer.scala:135)
  at kafka.consumer.SimpleConsumer$$anonfun$fetch$1$$anonfun$apply$mcV$sp$1.apply(SimpleConsumer.scala:135)
  at kafka.consumer.SimpleConsumer$$anonfun$fetch$1$$anonfun$apply$mcV$sp$1.apply(SimpleConsumer.scala:135)
  at kafka.metrics.KafkaTimer.time(KafkaTimer.scala:31)
  at kafka.consumer.SimpleConsumer$$anonfun$fetch$1.apply$mcV$sp(SimpleConsumer.scala:134)
  at kafka.consumer.SimpleConsumer$$anonfun$fetch$1.apply(SimpleConsumer.scala:134)
  at kafka.consumer.SimpleConsumer$$anonfun$fetch$1.apply(SimpleConsumer.scala:134)
  at kafka.metrics.KafkaTimer.time(KafkaTimer.scala:31)
  at kafka.consumer.SimpleConsumer.fetch(SimpleConsumer.scala:133)
  at kafka.consumer.ConsumerFetcherThread.fetch(ConsumerFetcherThread.scala:117)
  at kafka.consumer.ConsumerFetcherThread.fetch(ConsumerFetcherThread.scala:36)
  at kafka.server.AbstractFetcherThread.processFetchRequest(AbstractFetcherThread.scala:149)
  at kafka.server.AbstractFetcherThread.doWork(AbstractFetcherThread.scala:113)
  at kafka.utils.ShutdownableThread.run(ShutdownableThread.scala:64)

And, this error message repeats again and again.