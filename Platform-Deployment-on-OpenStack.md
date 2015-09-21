
# Prerequisites

> We constantly strive to make TAP deployment easier and more resilient to physical environment differences. Currently, TAP validated prerequisites include:

* Kilo (2015.1.0 or 2015.1.1) or Juno (2014.2.3) OpenStack
    * Mirantis Juno release ships with Openstack 2014.2.1 - this is supported, but requires [workarounds](https://github.com/trustedanalytics/platform-wiki/wiki/Mirantis-OpenStack-Deployment#manual-steps)
* Tenant with at least: 128vCPU, 256GB RAM, 2TB for Root, 2TB for Volumes
* Networking (neutron) configured with dedicated 4 floating IPs (not limited to random DHCP)
    * 4 NICs per machine are needed with GRE networking
* 1 domain (internal OK)
* Ability to access the following domains (proxy OK)

```
*.amazonaws.com
*.bintray.com
*.cloudera.com
*.github.com
*.githubusercontent.com
*.oracle.com
*.ubuntu.com
*.zabbix.com
*.gotapaas.eu
*.bosh.io
*.docker.io
*.rvm.io
*.cloudfront.net
*.fastly.net
*.gnupg.net
*.centos.org
*.fedoraproject.org
*.python.org
*.rubygems.org
*.travis-ci.org
*.rubini.us
```
## Deploying Openstack

The detailed installation procedure for Mirantis OpenStack, along with possible problem resolutions can be found [here](https://github.com/trustedanalytics/platform-wiki/wiki/Mirantis-OpenStack-Deployment).

## Configuration steps for new deployment
1. OpenStack is up and running, with Ubuntu/Centos images uploaded. 

  ``` 
wget https://s3-us-west-1.amazonaws.com/openstack-images-dp2/centos-6-x86_64.qcow2 -O centos-6.qcow2
  ``` 
  ``` 
wget https://s3-us-west-1.amazonaws.com/openstack-images-dp2/trusty-server-cloudingimg-amd64-disk1.img -O ubuntu-trusty.img
  ```
2. Log on with admin rights.
3. _Change default flavor setting (memory) of Openstack. (follow http://docs.openstack.org/user-guide-admin/cli_manage_flavors.html)_
9. Go to the identity panel and create a new project.
9. In _Project members_, add the user account you are currently using with member and admin permissions.
9. Set all quotas in the _Quota_ tab. Minimum quotas for Trusted Analytics platform: 25 volumes, 2 TB disk size. You can set “-1” for maximum size of cluster. After creating the new project, copy the project ID and save it in any editor. You will need it later.
9. Go to the _Identity_ panel and create a new user (for example, use _deploymentacc_ with password; e-mail address is not required) and add this user to your created project with member permissions. 
9. Log on to your deployment account, go to the _Project > Network > Network topology_ tab. Open Firebug (or any other tool) and select the network element. Find the ID, which should look like this: 
 
  ``` 
id_6e6058c8-4669-4b57-b787-9c19adecc3d2 
  ```
9. Copy the ID without "id_" prefix (ie: 6e6058c8-4669-4b57-b787-9c19adecc3d2) to a file with the project ID and copy the name of the network. 
9. The last thing you need is the IP address of the identity API. You can find it in _Project > Compute > Access & security > API access_. Copy the IP address to a file with the project ID. The IP address will look like this:

  ``` 
http://10.91.120.12:5000/v2.0 
  ```

## Local setup
For the first time please follow instructions from here: https://github.com/trustedanalytics/platform-wiki/wiki/Platform-Deployment-Procedure-local-setup


## Clone the repo
```
git clone git@github.com:trustedanalytics/terraform-openstack-intel.git
cd terraform-openstack-intel
cp terraform.tfvars.example terraform.tfvars
```

### Providing information for terraform-openstack-intel
You will need to create keys for CDH and Jumpbox. You can make it by using the _ssh-keygen_ command. 
Use any editor you like to edit the _terraform.tfvars_ file.
```
  1 auth_url="{ip of API}"
  2 tenant_name="{name of your project}"
  3 tenant_id="{id of your project – from step 1}"
  4 username="{account you created with members privileges}"
  5 password="{password to account}"
  6 jumpbox_public_key_path="{public jumpbox key}"
  7 jumpbox_key_path="{public jumpbox key}"
  8 cdh_key_path="{public jumpbox key}"
  9 cdh_public_key_path="{public jumpbox key}"
 10 floating_ip_pool="{name of network}"
 11 network_external_id="{id of network}"
 12 region="RegionOne"
 13 worker_size=3 # set worker size you want to have for cloudera
 14 master_size=3 # set master size you want to have for cloudera
 15 ansible_repo_path="{path to terraform-openstack-intel}" # i.e. $HOME/openstack/terraform-openstack-intel/
 16 ubuntu_image_name="ubuntu-trusty" #changes with every deploy
 17 centos_image_name="centos-6" # changes with every deploy
 18 http_proxy="{set proxy}" # comment if not needed
 19 https_proxy="{set proxy}" # comment if not needed
 20 dns1="{dns server}" # comment if not needed
 21 dns2=”{dns server}" # comment if not needed
```
After you set all variables, run the following:
```
make update
make plan
make apply
make provision
```

Because OpenStack resources aren’t always available when requested, don’t panic if an error occurs while the _make apply_ or _make provision_ commands execute. Just run the command again.

## Set up bosh deployment
Follow the instructions from this document: https://github.com/trustedanalytics/platform-wiki/wiki/Platform-Deployment-Procedure:-bosh-deployment