> In addition to the managed data services in [[Application Platform]], Trusted Analytics platform includes also a core data hub built on top of Cloudera's distribution of Hadoop (CDH). 

## CDH

This Analytics PaaS includes a scalable [Cloudera Enterprise Data Hub](https://s3.amazonaws.com/quickstart-reference/cloudera/hadoop/latest/doc/Cloudera_EDH_on_AWS.pdf). The main components of this PaaS are: 

* HBase
* HDFS 
* [Kafka](Kafka Cluster)
* Spark
* YARN
* Zookeeper

## Cloudera Director

In addition to core CDH components, Trusted Analytics platform also includes recently released [Cloudera Director](http://www.cloudera.com/content/cloudera/en/products-and-services/director.html). The core objective for deploying Director within Trusted Analytics platform is to enable operations and provide:

* Self-service cluster spinup/teardown capability
* Dynamic scaling for unpredictable workloads

> Cloudera Director is not exposed to end-users; only Trusted Analytics platform Operators have access to it.



