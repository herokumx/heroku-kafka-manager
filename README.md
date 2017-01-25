Heroku Kafka Manager
=====================

A tool for managing [Heroku Kafka](https://www.heroku.com/kafka) that works in Heroku Common Runtime or Private Spaces!

[![Deploy](https://www.herokucdn.com/deploy/button.svg)](https://heroku.com/deploy?template=https://github.com/herokumx/heroku-kafka-manager)

It supports the following :

 - Manage multiple clusters
 - Easy inspection of cluster state (topics, consumers, offsets, brokers, replica distribution, partition distribution)
 - Run preferred replica election
 - Generate partition assignments with option to select brokers to use
 - Run reassignment of partition (based on generated assignments)
 - Create a topic with optional topic configs (0.8.1.1 has different configs than 0.8.2+)
 - Delete topic (only supported on 0.8.2+ and remember set delete.topic.enable=true in broker config)
 - Topic list now indicates topics marked for deletion (only supported on 0.8.2+)
 - Batch generate partition assignments for multiple topics with option to select brokers to use
 - Batch run reassignment of partition for multiple topics
 - Add partitions to existing topic
 - Update config for existing topic
 - Optionally enable JMX polling for broker level and topic level metrics.
 - Optionally filter out consumers that do not have ids/ owners/ & offsets/ directories in zookeeper.

Cluster Management

![cluster](https://s3.amazonaws.com/herokumximages/kafkamgr/cluster.png)

***

Topic List

![topic](https://s3.amazonaws.com/herokumximages/kafkamgr/topic-list.png)

***

Topic View

![topic](https://s3.amazonaws.com/herokumximages/kafkamgr/topic.png)

***

Consumer List View

![consumer](https://s3.amazonaws.com/herokumximages/kafkamgr/consumer-list.png)

***

Consumed Topic View

![consumer](https://s3.amazonaws.com/herokumximages/kafkamgr/consumed-topic.png)

***

Broker List

![broker](https://s3.amazonaws.com/herokumximages/kafkamgr/broker-list.png)

***

Broker View

![broker](https://s3.amazonaws.com/herokumximages/kafkamgr/broker.png)

***

Requirements
------------

1. [Heroku Kafka 0.10.0.1](https://elements.heroku.com/addons/heroku-kafka)
2. Java 8+

Configuration
-------------

The minimum configuration is the zookeeper hosts which are to be used for kafka manager state.
This can be found in the application.conf file in conf directory.  The same file will be packaged
in the distribution zip file; you may modify settings after unzipping the file on the desired server.

    kafka-manager.zkhosts="my.zookeeper.host.com:2181"

You can specify multiple zookeeper hosts by comma delimiting them, like so:

    kafka-manager.zkhosts="my.zookeeper.host.com:2181,other.zookeeper.host.com:2181"

Alternatively, use the Heroku Config Variable `ZK_HOSTS` if you don't want to hardcode any values.

    ZK_HOSTS="my.zookeeper.host.com:2181"

You can optionally enable/disable the following functionality by modifying the default list in application.conf :

    application.features=["KMClusterManagerFeature","KMTopicManagerFeature","KMPreferredReplicaElectionFeature","KMReassignPartitionsFeature"]

 - KMClusterManagerFeature - allows adding, updating, deleting cluster from Kafka Manager
 - KMTopicManagerFeature - allows adding, updating, deleting topic from a Kafka cluster
 - KMPreferredReplicaElectionFeature - allows running of preferred replica election for a Kafka cluster
 - KMReassignPartitionsFeature - allows generating partition assignments and reassigning partitions

Consider setting these parameters for larger clusters with jmx enabled :

 - kafka-manager.broker-view-thread-pool-size=< 3 * number_of_brokers>
 - kafka-manager.broker-view-max-queue-size=< 3 * total # of partitions across all topics>
 - kafka-manager.broker-view-update-seconds=< kafka-manager.broker-view-max-queue-size / (10 * number_of_brokers) >

Here is an example for a kafka cluster with 10 brokers, 100 topics, with each topic having 10 partitions giving 1000 total partitions with JMX enabled :

 - kafka-manager.broker-view-thread-pool-size=30
 - kafka-manager.broker-view-max-queue-size=3000
 - kafka-manager.broker-view-update-seconds=30

The follow control consumer offset cache's thread pool and queue :

 - kafka-manager.offset-cache-thread-pool-size=< default is # of processors>
 - kafka-manager.offset-cache-max-queue-size=< default is 1000>
 - kafka-manager.kafka-admin-client-thread-pool-size=< default is # of processors>
 - kafka-manager.kafka-admin-client-max-queue-size=< default is 1000>

You should increase the above for large # of consumers with consumer polling enabled.  Though it mainly affects ZK based consumer polling.

Heroku Kafka managed consumer offset is now consumed by KafkaManagedOffsetCache from the "__consumer_offsets" topic.  Note, this has not been tested with large number of offsets being tracked.  There is a single thread per cluster consuming this topic so it may not be able to keep up on large # of offsets being pushed to the topic.

Deployment
----------

Click the Heroku Button and let it go...

[![Deploy](https://www.herokucdn.com/deploy/button.svg)](https://heroku.com/deploy?template=https://github.com/herokumx/heroku-kafka-manager)

Credits
-------

This app is made possible by [Yahoo's Kafka Manager on Github](https://github.com/yahoo/kafka-manager).

Most of the utils code has been adapted to work with [Apache Curator](http://curator.apache.org) from [Apache Kafka](http://kafka.apache.org).


License
-------

Heroku & Apache Licensed. See accompanying LICENSE file.
