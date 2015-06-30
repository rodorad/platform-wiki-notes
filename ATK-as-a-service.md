## User experience

The Analytics Toolkit (ATK) Software Server will be available via the CF Marketplace, and isolated instances (with dedicated CDH data slices) are created using the CF CLI:

    cf target -o intel -s playtime
    cf create-service atk free my-atk

In the background, the toolkit server is deployed to that user's space (intel/playtime) along with slices of its CDH dependencies: HBase, HDFS, and PostgreSQL.

## ATK on Cloud Foundry

Provisioning ATK involves:

* Creating an HBase service instance. 
* Creating an HDFS service instance. 
* Creating a PostgreSQL service instance. 
* Pushing ATK Server app.

These items are visible to the end-user. They are created/pushed into their own org/space. (For example, `intel/playtime` above.)

## ATK Service Broker

The [app-launching-service-broker](https://github.com/trustedanalytics/app-launching-service-broker) manages the entire `atk` lifecycle:

> Note, all broker operations (provision, deprovision, etc.) on _cf_ are performed in context of a unique _CF_HOME_, to ensure some resemblance to command multitenancy. 

### Provision

    cf target -o intel -s playtime
    cf push my-atk-app -p /path/app/src --no-start
    cf create-service hbase free my-atk-app-hbase
    cf create-service hdfs free my-atk-app-hdfs
    cf create-service postgresql93 free my-atk-app-postgresql93

### Deprovision 

    cf target -o intel -s playtime
    cf delete my-atk-app -f
    cf delete-service my-atk-app-hbase -f
    cf delete-service my-atk-app-hdfs -f
    cf delete-service my-atk-app-postgresql93 -f

### Caveats

In order to get the service instance name, organization name, and space name, the service broker needs to reverse-look up these records from the Cloud Foundry API.

The following illustrates the basic queries:

    cf curl /v2/organizations/{GUID}/summary
    cf curl /v2/spaces/{GUID}/summary
    cf curl /v2/service_instances/{GUID}

### Registering Service Broker

    cf create-service-broker SERVICE_BROKER USERNAME PASSWORD URL
    cf enable-service-access SERVICE_BROKER -p free -o intel
