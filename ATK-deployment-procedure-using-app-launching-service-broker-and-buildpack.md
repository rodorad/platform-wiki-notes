#### Download and configure app-launching-service-broker
9. Clone the app-launching-service-broker and go to the downloaded directory:

    ```bash
    git clone git@github.com:trustedanalytics/app-launching-service-broker.git <broker-app-name>
    cd <broker-app-name>
    ```
9. Run the _fetch_ command to download the required cf-cli binaries:

    ```
    ./bin/fetch_cf_cli.sh
    ```
9. Open _manifest.yml_ then edit the broker name (which must be unique, for example, _atk-broker_) and add _env_ values:

    ```
    CF_API: http://api.<domain>
    CF_DEP: postgresql93|free,cdh|shared,zookeeper|shared
    CF_SRC: ./apps/atk
    CF_USER: <user, eg. admin>
    CF_PASS: <password_for_CF_USER> 
    ```
9. The manifest after that should look like this:

    ```
    ---
    applications:
    - name: atk-broker
      memory: 256M
      instances: 1
      path: .
      buildpack: go_buildpack
      env:
        CF_API: http://api.domain.com
        CF_CATALOG_PATH: ./catalog.json
        CF_DEP: postgresql93|free,cdh|shared,zookeeper|shared
        CF_SRC: ./apps/atk
        CF_USER: admin
        CF_PASS: pass_for_CF_USER
    ```
1. Open _catalog.json_ and edit the values as follows:

    - services -> id (must be unique in the environment)
    - services -> name (must be unique; for example, atk*)
    - services -> description
    - services -> tags
    - services -> plans -> id (must be unique in the environment)

    Example catalog.json:
    ```
    {
      "services": [{
        "id": "c00be1df-89ce-4d42-8b21-644afaf41121",
        "name": "atk",
        "description": "Intel Analytics Toolkit",
        "bindable": true,
        "tags": ["atk", "analytics"],
        "plans": [{
          "id": "04eb29c9-5b17-4b70-9740-9d9e9cf20db1",
          "name": "simple",
          "description": "Simple",
          "free": true
        }]
      }]
    }
    ```

#### Create ATK-required files

9. Create the ```apps/atk/``` directory inside the app-launching-service-broker. (Use the name you specified during cloning.)

    - Add _apps/atk/manifest.yml_. It should look like this. (You can copy the following without changing anything.)

    ```
    ---
    applications:
    - name: atk # must be the same as services -> name in catalog.json
      command: bin/rest-server.sh
      memory: 1G
      disk_quota: 2G
      timeout: 180
      instances: 1
      buildpack: atk-buildpack
    ```
    - Add empty atk file into apps/atk/
    - Edit bin/rest-server.sh. This file have two values specific to Ireland environment (if you want to create ATK broker in Ireland skip this step):
    ```
    export CC_URI=$(echo $VCAP_APPLICATION | $jq  '.application_uris[0]' | sed -e "s/$APP_NAME\.apps/http:\/\/api.run/g" | tr -d '"')
    export UAA_URI=$(echo $VCAP_APPLICATION | $jq  '.application_uris[0]' | sed -e "s/$APP_NAME\.apps/http:\/\/uaa.run/g" | tr -d '"')
    ```
9. Remove '\.apps' and '.run'. After that, these lines should look like this:

    ```
    export CC_URI=$(echo $VCAP_APPLICATION | $jq  '.application_uris[0]' | sed -e "s/$APP_NAME/http:\/\/api/g" | tr -d '"')
    export UAA_URI=$(echo $VCAP_APPLICATION | $jq  '.application_uris[0]' | sed -e "s/$APP_NAME/http:\/\/uaa/g" | tr -d '"')
    ```
9. Before the push, you will need to add three variables for spark inside _conf/application.conf_:

    ```
    spark.eventLog.overwrite = "true"
    spark.eventLog.enabled = "true"
    spark.eventLog.dir = "hdfs://ip-10-10-10-236.us-west-2.compute.internal:8020/user/spark/applicationHistory"
    ```
    Where the example value _ip-10-10-10-236.us-west-2.compute.internal_ is the hostname of hdfs namenode.

#### Push broker

Go to the broker's main directory and push the broker with ```cf push```. This will create a service broker application.If it is the first push of _atk-broker_, you need to create a service broker and enable access to this service. (```<service_broker_name>``` should be unique. For example, atk*, ```<your_service_name>``` should be the same as services -> name in catalog.json or check it using cf service-access):

    ```
    cf create-service-broker <service_broker_name> admin admin http://<app-url>
    cf enable-service-access <your_service_name>
    ```
If you had atk-broker before and it is the only update, run the update command:

    ```
    cf update-service-broker <service_broker_name> admin admin http://<app-url>
    ```
#### Create an ATK instance
There are two ways to create an ATK instance:
- Use the console.
- Use the command line (like this): `cf cs <service_broker_name> simple <atk-instance-name>`

NOTE: The name of the created app will be different from ```<atk-instance-name>```. You can check the app name in the console.

#### Verify ATK client connection

To verify that the ATK Server works properly, do the following:

9. Install the ATK client and try to connect it to the ATK server. 

9. Open [ATK Client Installation Configuration and Usage](ATK-Overview#client-installation-configuration-and-usage) page.
