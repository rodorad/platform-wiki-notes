
# Prerequisites
## OpenStack configuration steps for new deployment
1. OpenStack is up and running, with Ubuntu/Centos images uploaded. 
9. Log on with admin rights.
9. Go to the identity panel and create a new project.
9. In _Project members_, add the user account you are currently using with member and admin permissions.
9. Set all quotas in the _Quota_ tab. Minimum quotas for Trusted Analytics platform: 25 volumes, 2 TB disk size. You can set “-1” for maximum size of cluster. After creating the new project, copy the project ID and save it in any editor. You will need it later.
9. Go to the _Identity_ panel and create a new user (for example, use _deploymentacc_ with password; e-mail address is not required) and add this user to your created project with member permissions. 
9. Log on to your deployment account, go to the _Project > Network > Network topology_ tab. Open Firebug (or any other tool) and select the network element. Find the ID, which should look like this: 
 
  ``` 
id_6e6058c8-4669-4b57-b787-9c19adecc3d2 
  ```
9. Copy the ID to a file with the project ID and copy the name of the network. 
9. The last thing you need is the IP address of the identity API. You can find it in _Project > Compute > Access & security > API access_. Copy the IP address to a file with the project ID. The IP address will look like this:

  ``` 
http://10.91.120.12:5000/v2.0 
  ```

# Local configuration
If you have a device with Linux* OS, you will need to install some software:
-	Git
-	Terraform v0.5.3 

## Install Git
Follow the instructions [from the git-scm.com website to install git](http://git-scm.com/book/en/v2/Getting-Started-Installing-Git). A short summary is:
- RHEL/CentOS/Fedora - “sudo yum install -y git”
- Ubuntu/Debian = “sudo apt-get install -y git”

## Install Terraform
1. Visit the [terraform website](https://www.terraform.io/downloads.html) to download a version of Terraform for your local computer. 

9. You need to use a specific version of Terraform; the latest supported version of Terraform is v0.5.3.  Download this version of Terraform from [dl.bintray.com source](https://dl.bintray.com/mitchellh/terraform/terraform_0.5.3_linux_amd64.zip). 

9. After you download the appropriate zip, copy the files to a folder in your PATH, in the example below we use _~/bin_.

    ```
unzip terraform_0.5.3_linux_amd64.zip -d ~/bin
    ```
9. Open a new terminal window and run:

    ```
terraform -v
    ```

9. You should get the following output:

    ```
Terraform v0.5.3
    ```

## Clone the repo
1. On your local computer run the following:

    ```bash
-
Add config file ~/.ssh/config
vi ~/.ssh/config
add following lines
Host github.com
    User                    git
-
create ssh key
ssh-keygen -t rsa

it will create pair of keys in ~/.ssh directory
id_rsa
id_rsa.pub

Log on to github account using any browser
create ssh-key on github account using content os id_rsa.pub

-
ALSO make sure your github account has access to 
trustedanalytics/terraform-openstack-intel.git repository.
If you don't have access to this repository, next step will fail.

-
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
For the _trustedanalytics/docker-services-boshworkspace_ repository until it will be opensourced (made public) there is a need to use github oauth token (user:token) below should be added to variable file:
```
gh_auth="user:token"
```

After you set all variables, run the following:
```
make update
make plan
make apply
make provision
```

Because OpenStack resources aren’t always available when requested, don’t panic if an error occurs while the _make apply_ or _make provision_ commands execute. Just run the command again.

## Manual steps (not yet automated)
Log on to the box shown as bastion_ip

```
ssh -i ~/.ssh/bosh.pem ubuntu@bastion_ip
```

### Remove apps/run from the domain
Open the deployment file and remove apps from app_domains and run from domain:
```bash
cd ~/workspace/deployments/cf-boshworkspace/
vim deployments/cf-openstack-tiny.yml
```
Change target to cf-openstack-tiny and deploy:
```bash
bosh deployment cf-openstack-tiny
bosh deploy
```
Lastly, remove the old domain. To do this, list all domains and remove the domain with apps/run:
```bash
cf domains
cf delete-shared-domain [domain_name]
```
### Add clients
Edit template (templates/cf-properties.yml).
```bash
cd ~/workspace/deployments/cf-boshworkspace
vim templates/cf-properties.yml
```
Add this to path properties > uaa > clients:
```
      developer_console:
        override: true
        scope: cloud_controller.admin,cloud_controller.read,cloud_controller.write,console.admin,doppler.firehose,openid,password.write,scim.read,scim.userids,scim.write
        authorities: scim.write,scim.read,cloud_controller.read,cloud_controller.write,password.write,uaa.admin,uaa.resource,cloud_controller.admin,billing.admin
        secret: (( merge ))
        authorized-grant-types: authorization_code,password,implicit,client_credentials,refresh_token
        access-token-validity: 1209600
        refresh-token-validity: 1209600
        redirect-uri: (( "http://console." domain "/oauth/callback,https://console." domain "/oauth/callback" ))
        autoapprove: true

```
Deploy changes:
```bash
cd ~/workspace/deployments/cf-boshworkspace
bosh deployment cf-openstack-tiny
bosh deploy
```
## Push apps and services (we are working to automate it)
### Log on to Cloud Foundry
Log on to Cloud Foundry using an admin account and create organization and space.
```bash
cf api http://api.<domain>
cf login
cf create-org <org_name>
cf target -o <org_name>
cf create-space <space_name>
cf target -s <space_name>
```
### Create docker-broker
Check the URL for docker in _~/workspace/deployments/docker-services-boshworkspace/deployments/docker-openstack-vpc.yml_. Path is meta -> cfcontainersbroker -> external_host
And run command to add service-broker.
```bash
update nats/machine address in docker-openstack-vpc.yml
This address can be found in /workspace/deployments/cf-boshworkspace/.deployments/cf-openstack-tiny.yml 
  nats->machines
- remove "run." on the cc_api_uri and external_host if present.

cf create-service broker <name> <user> <password> <broker_url>
# eg:
# cf create-service-broker docker containers containers http://cf-containers-broker.domain.com
```
Enable access to services provided by docker broker. To do this check available services:
```bash
cf service-access
```
Now you can enable access to all required services:
```bash
cf enable-service-access <service_name>
```

#### Change login server
Make sure that you have latest gems:
```
cd ~/workspace/deployments/cf-boshworkspace/
bundle update
```

Add two templates and credentials:
- templates/cf-project-login.yml
```
meta:
  app_domains: (( merge ))
  domain: (( merge ))
  login_smtp:
    host: (( merge ))
    port: (( merge || 465 ))
    user: (( merge ))
    password: (( merge ))
    senderEmail: (( merge ))
    senderName: (( merge ))

properties:
  login:
    asset_base_url: /resources/project
    tiles:
      - name: Developer console
        login-link: (( "https://console." meta.app_domains ))
        image: /resources/project/images/console.png
        image-hover: /resources/project/images/console.png
    smtp:
      host: (( meta.login_smtp.host ))
      port: (( meta.login_smtp.port ))
      user: (( meta.login_smtp.user ))
      password: (( meta.login_smtp.password ))
      senderEmail: (( meta.login_smtp.senderEmail ))
      senderName: (( meta.login_smtp.senderName ))
    links:
      passwd: (( "https://login." meta.domain "/forgot_password" ))
    self_service_links_enabled: true
```
- templates/cf-project-login-additional-properties.yml
```
properties:
  login:
    <<: (( merge ))
    smtp:
      host: (( merge ))
      port: (( merge ))
      user: (( merge ))
      password: (( merge ))
      senderEmail: (( merge ))
      senderName: (( merge ))
```
- .credentials.yml
```
- url: https://github.com/trustedanalytics/login-boshrelease.git
  username: YOUR_USERNAME
  password: YOUR_PASS
```

Edit another template: templates/tiny/cf-tiny-scalable.yml in path meta -> services_templates and replace login with:
```
  - name: login-intel
    release: login-intel
```

Edit deployment file: deployments/cf-openstack-tiny.yml:
- add two templates
```
- cf-project-login-additional-properties.yml # before cf/cf-properties.yml
- cf-project-login.yml # bottom of template list
```
- add release:
```
  - name: login-intel
    version: 5
    ref: 31e3307268 # can be removed when version 6 is release
    git: https://github.com/trustedanalytics/login-boshrelease.git
```
- add configuration to meta:
```
  login_smtp:
    host: mail.YOURSMTP.com
    port: 2525
    user: YOURUSER
    password: YOURPASS
    senderEmail: support@domain.com
    senderName: Domain
```

Increase the number of _runner_ VMs to **3** in the _cf-openstack-tiny.yml_ file:
```sh
vim ~/workspace/deployments/cf-boshworkspace/deployments/cf-openstack-tiny.yml
```

```yml
...
meta:
...
  instances:
...
    runner_z1:    3 # MARKER_FOR_PROVISION
...
```

Change the disk size to **523552 MB** in the _cf-properties.yml_ file:
```sh
vim ~/workspace/deployments/cf-boshworkspace/templates/cf-properties.yml
```

```yml
...
properties:
...
  dea_next:
...
    disk_mb: 523552
...   
```
Now you are ready to deploy new login server
```bash
cd ~/workspace/deployments/cf-boshworkspace/
bosh deployment cf-openstack-tiny
bosh prepare deployment
bosh deploy
```

Update the maximum amount of memory to 30 GB:
```
cf update-quota default -m 30G
```
## UAA changes
Do these commands on your bastion server, at _/workspace/deployments/cf-boshworkspace directory_.
```
uaac target uaa.{uaa IP}
uaac token client get {user} -s {password}
uaac group add console.admin
uaac group add console
uaac member add console.admin {user}
uaac member add console admin
uaac client add atk-client --scope cloud_controller.read,cloud_controller.write,cloud_controller_service_permissions.read,openid --authorized_grant_types authorization_code,password,refresh_token --redirect_uri http://{atk-dash IP}/managed/auth/cloudfoundry/callback --authorities uaa.none
```

## Push apps and services
You need to have access to a blob store with the projects artifacts because the tool downloads them from it.

Clone two repos:
```bash
git@github.com:trustedanalytics/cloudfoundry-mkappstack.git
git@github.com:trustedanalytics/platform-appstack.git
```
Copy files from platform-appstack to cloudfoundry-mkappstack:
```bash
cp platform-appstack/demo-platform.yml cloudfoundry-mkappstack/appstack.yml
cp platform-appstack/demo-settings.yml cloudfoundry-mkappstack/settings.yml
```
Go to cloudfoundry-mkappstack directory and copy templates
```bash
cd cloudfoundry-mkappstack
cp appstack.mk.tmpl appstack.mk
cp secret.mk.tmpl secret.mk
```
Enter your environment information to secret.mk.

Open appstack.mk and change artifact_pfx to the store url. Uncomment proxy and noproxy and put correct proxy values there.

Last file to edit is _settings.yml_. Complete missing values using your environment information.

> **NOTE:** Generate HADOOP_PROVIDED_PARAMS using this instruction: _https://github.com/trustedanalytics/hdfs-broker#injection-of-hdfs-client-configuration_.

You are ready to deploy whole platform:
```bash
make deploy
```
