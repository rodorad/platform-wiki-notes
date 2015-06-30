Enter the following command to display a list of already deployed primitives:

    cf marketplace

For API connectivity, see the [[FAQs]].  

## Service types

Provided services fall into two groups:

* Managed
* Integrated 

Managed services tend to be based on multinode architectures, whereas integrated services are more commonly found on single-node architectures.

## Managed services 

Managed services live outside of the CF deployment itself. Their resources are "sliced" on demand by the CF service broker. A perfect example of a managed service is a Hadoop cluster where individual users of the platform can get their own account (slice). 

## Integrated services 

Integrated services, by contrast, are managed by a CF instance. The Service Broker corresponding to each of these services creates the dependent services and binds them to the requested application. 

## Target list

* Time series
  - [Apache HBase](http://hbase.apache.org/) (M)
* Column
  - [HBase](http://hbase.apache.org/) (M)
  - [Apache Cassandra](http://cassandra.apache.org/) (I)
* Document
  - [MongoDB](http://www.mongodb.org/) (I)
* Graph
  - [Titan](http://thinkaurelius.github.io/titan/) (I)
* KeyValue
  - [Redis](http://redis.io/) (I)
  - [etcd](https://github.com/coreos/etcd) (I)
  - [consul](http://www.consul.io/intro/getting-started/kv.html) (I)
* Message
  - [Apache Kafka](http://kafka.apache.org/)  (M)
  - [RabbitMQ](http://www.rabbitmq.com/) (M)
* Object
  - [RiakCS](http://basho.com/riak-cloud-storage/) (I)
* SQL
  - [MySQL](http://www.mysql.com/) (I)
  - [PostgreSQL](http://www.postgresql.org/) (I)
  - [Apache SparkSQL](https://spark.apache.org/sql/) (M)
  - [Impala](http://www.cloudera.com/content/cloudera/en/products-and-services/cdh/impala.html) (M)
  - [Apache Hive](https://hive.apache.org/) (M)
* Stream
  - [Apache Spark Streaming](https://spark.apache.org/streaming/) (M)
  - [DataTorrent](https://www.datatorrent.com/) (M)