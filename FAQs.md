## App and Deployment FAQs

**Q: How do I deploy an example Java app?**<br />
    ```
    git clone https://github.com/cloudfoundry-samples/spring-music.git
    cd spring-music
    ./gradlew assemble
    cf push
    ```

If you are developing your own application with maven:

    vim manifest.yml # see https://github.com/cloudfoundry-samples/spring-music/blob/master/manifest.yml for reference
    mvn clean package
    cf push -n [hostname] # your app will be visible under [hostname].domain.com
   

**Q: My app didn't run on CF. What should I do?**

* Check if your jar is working:<br>
    `java -jar target/[jar-name].jar`

* Check cf logs<br>
    `cf logs [app-name] --recent`

* If you get _ERR Instance (index 0) failed to start accepting connections_...
    - try to change the memory in manifest to 512 MB or 1 GB; or 
    - verify that you didn't use a specific port.

**Q: How do I provision a sample (PostgreSQL) service?**<br />

    cf create-service postgresql93 free spring-music-pg
    cf bind-service spring-music spring-music-pg
    cf restart spring-music

**Q: How can I get a shell session for my app?** <br />

You can get a new container in Cloud Foundry containing your application code with the _cf-ssh_ tool. See https://blog.starkandwayne.com/2014/07/14/running-one-time-tasks-in-cloud-foundry/

**Q: How do I run warden on the runner machine to troubleshoot a container?**

On the jump box:

    `bosh ssh runner_z1/0`
    `export PATH=/var/vcap/packages/ruby/bin:$PATH`
    `cd /var/vcap/data/packages/warden/<TAB>/warden`
    `./bin/warden --socket /var/vcap/data/warden/warden.sock`

From that point, you can do the things as specified in [the Cloud Foundry troubleshooting docs] (http://docs.cloudfoundry.org/running/troubleshooting/troubleshooting-warden-services.html).

**Q: Where do I get cf-ssh?** <br />

You can read a [blog post about it](https://blog.starkandwayne.com/2014/07/14/running-one-time-tasks-in-cloud-foundry/) or you can just do the following:

```
cd ~/bin  
wget https://raw.githubusercontent.com/danhigham/tmate-bootstrap/master/scripts/cf-ssh -O cf-ssh  
chmod +x cf-ssh 
```

**Q: How do I view the current memory and disk usage of Cloud Foundry?**<br />

Log on to the jumpbox and run the _dea_ads_ command.

**Q: How do I view all the current logs within Cloud Foundry?**<br />

Log on to the jumpbox and run the _nats_sub_ command.

**Q: What languages and frameworks are inherently supported with Trusted Analytics Platform?**<br />
Java,python,nodejs, scala, go, ruby,PHP
Language	Runtime	Framework
Java	Java 6, Java 7, Java 8	Spring Framework 3.x, 4.x
Ruby	Ruby 1.8, Ruby 1.9, Ruby 2.0	Rails, Sinatra
Node.js	V8 JavaScript Engine (from Google Chrome)	Node.js
Scala	Scala 2.x	Play 2.x, Lift
Python		Python
PHP		PHP

              
**Q: Does Trusted Analytics Platform support all common buildpacks traditionally supported with Cloud Foundry?**<br />
YES
java_buildpack           1          true      false    java-buildpack-v3.0.zip   

ruby_buildpack           2          true      false    ruby_buildpack-cached-v1.3.0.zip   

nodejs_buildpack         3          true      false    nodejs_buildpack-cached-v1.2.1.zip   

go_buildpack             4          true      false    go_buildpack-cached-v1.2.0.zip   

python_buildpack         5          true      false    python_buildpack-cached-v1.2.0.zip   

php_buildpack            6          true      false    php_buildpack-offline-v3.1.0.zip   

staticfile_buildpack     10         true      false    staticfile_buildpack-cached-v1.0.0.zip   

 
**Q: What is the dependency of Trusted Analytics Platform on CDH?**<br />
In case customer needs big data capabilities, or the Analytics Toolkit*, it needs to have CDH. At this moment we don’t support any other distribution.
This can be deployed without CDH
CDH provides big data capabilities to this platform . ATK also need CDH

**Q: What tools can be used with the Analytics Toolkit*?**<br />
The Analytics Toolkit* can be accessed using python. The Analytics Toolkit* also requires CDH with spark running on it.
The Analytics Toolkit* currently runs on CDH 5.3.1

**Q: What data is supported in the Console > Data Catalog > Submit Transfer?**<br />
Any data set that  is publicly available via http protocol, can be downloaded to DP2 platform.
There are additional ways to getting data.
Client (Data producers) —>Websocket —> Kafka —> HDFS
Client(Data producers) —> MQTT broker —> HDFS

**Q: Where can I find a list of all supported CF commands?**<br />
All cloudfoundry  cli commands documentation can be found www.cloudfoundry.org
Also cf —help will list all available commands

## Admin and User Management FAQs

**Q: In Console > User management, what is the difference between “space” and “organization”?**<br />
Hierarchy is organization —> space
Space may be considered as dept within an organization.
There is no fixed meaning to space.
Some places space can “dev” , “test” , “prod”
Or some places it can different departments within organization
Only "space developer" can deploy apps

**Q: How do I add new services to Console > Marketplace?**<br />
There are cloud foundry commands to add services to Marketplace.
Please refer to cloud foundry documentation for development of new services. (how to code a new service)

**Q: How much time should I give an app to re-stage?**<br />
It depend on application startup procedure. If the application startup procedure is long it will take time.

**Q: How do I configure an app to automatically re-stage or deploy another instance of itself?**<br />
- Write scripts on unix to automatically re-stage using cf command line.
- There is automatic pay increase or decrease instance. Cf command line can be used to deploy another instance.
- There are ample opportunity to write application specific scaling script

## Open Source FAQs

**Q. What is the governance model of this project?****<br />

It is our intention in Trusted Analytics Platform engineering community to follow an open and democratic model found in many of the Apache Foundation's open source projects. Model based on a earned leadership. 

As developers demonstrate their abilities by contributing new code and documentation to the Trusted Analytics project and their contributions are acknowledged by the existing members by pulling their requests, the individual contributor will gain greater responsibility in decision-making of our project. (See “How the Apache Software Foundation Works”)

**Q. How frequently are updates made?**

Eh, with every commit? Not sure how applicable this is in an open source project managed in GitHub.


**Q. What part of the project needs the most contributions?**

Analytical Tooling 

* [The Analytics Toolkit](https://github.com/trustedanalytics/atk)
* [The Analytics Toolkit Buildpack](https://github.com/trustedanalytics/atk-buildpack)

Installer Tools

* [Platform Parent](https://github.com/trustedanalytics/platform-parent)

Others

* [App Launching](https://github.com/trustedanalytics/app-launching-service-broker)
* [Service Catalog](https://github.com/trustedanalytics/service-catalog)
* [Data Acquisition](https://github.com/trustedanalytics/data-acquisition)

**Q. What elements of this project are in use by other projects?**

Many of the ~30 projects comprising Trusted Analytics platform depend on each other while some can stand on its own. All of the projects are developed in a way that should minimize the effort to use them independent.