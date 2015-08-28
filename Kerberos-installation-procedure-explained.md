The ansible scripts that DP2 is using to boostrap Kerberos on the CDH cluster we are using are loosely based on two things:
- The official documentation on [Cloudera Kerberos manual](http://www.cloudera.com/content/cloudera/en/documentation/core/latest/topics/cm_sg_intro_kerb.html)
- An open source script for bootstraping the CDH cluster found [here](https://github.com/gdgt/cmapi/blob/master/cmxDeploy.py#L879-L928)

They are divided into three main steps:
### Setting up the server 

The ansible file can be found [here](https://github.com/trustedanalytics/platform-ansible/blob/master/roles/kerberos_base_server/tasks/main.yml).

It creates the the kerberos server machine, installing the appropriate packages and creating configuration files from templates. After that, it creates the initial users.

### Setting up the clients 

The ansible file can be found [here](https://github.com/trustedanalytics/platform-ansible/blob/master/roles/kerberos_base_common/tasks/main.yml).

It installs the client libraries on every machine and creates the `krb5.conf` file that is used by applications to get the kerberos setting in a given environment.

### Enabling Kerberos support on the CDH cluster 
The ansible file can be found [here](https://github.com/trustedanalytics/platform-ansible/blob/master/roles/cloudera_api_manager/tasks/kerberos.yml).

Since there is no one API call to enable Kerberos support, we are enabling it for each service and then generating the appropriate credentials. The whole process is available as a one-click wizard in the Cloudera Manager web interface.
