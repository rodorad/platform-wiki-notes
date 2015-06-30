### Cloud Foundry

The core application platform capabilities in this Analytics PaaS is delivered through custom deployment of [Cloud Foundry (CF)](http://docs.cloudfoundry.org/concepts/architecture/) and is composed of the following components:

* Director
* Blob store
* Workers
* Message bus
* Health monitor
* Agents
* Cloud platform integration
  - AWS
  - OpenStack (not validated)

### CLI

This Analytics PaaS is API-compatible with the open source release of CF and supports the [Command Line Interface (CLI) client for Cloud Foundry](https://github.com/cloudfoundry/cli) (also OSS).

You can find further documentation CLI [here](http://docs.cloudfoundry.org/devguide/#cf). There is also help available in the CLI client itself. Type `cf help` for more information. Each command also has help output available via `cf [command] --help` or `cf [command] -h`.
  
### Managed Services 

In addition to the core platform capabilities offered by CF, the platform also includes a growing set of managed services. These are added as needed: 

* Key/value stores
  - Consul
  - Redis
  - etcd
* Document stores
  - Elasticsearch
  - MongoDB
  - CouchDB
  - RethinkDB
* Relational stores
  - MySql
  - PostgreSQL
* Memcache stores
  - Memcached
* Graph stores
  - Neo4j
* Message queues
  - RabbitMQ
  - Kafka
  - Nats
* Other 
  - Logstash (logs collection)
  - SMTP (email sending)
  - ArangoDB (multipurpose NoSQL memory DB)

These services are managed by the platform and are dedicated in most cases. They support a set of predefined plans (for example, small, medium, and large) which can be customized for each deployment.
 
### Shared Services 

In addition to managed services outlined above, the platform also delivers a set of shared services on top of [[Data Platform]] in a multinode architecture:

* HBase
* HDFS 
* Zookeeper
* YARN
* Spark
* [Kafka](Kafka Cluster)

These multitenant and scalable services are provisioned by the individual service brokers and expose a "slice" of the shared resource pool in the [[Data Platform]] during binding process. 











