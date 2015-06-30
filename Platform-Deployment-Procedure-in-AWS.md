> [Wiki](Home) ▸ **Platform Deployment in AWS**

## Prerequisites
### GitHub Configuration

You should be capable to clone several GitHub repositories. If any of the repositories is private, you need to have a GitHub account, that is capable to access following repositories:

    https://github.com/trustedanalytics/cloudfoundry-mkappstack
    https://github.com/trustedanalytics/docker-services-boshworkspace
    https://github.com/trustedanalytics/hdfs-broker
    https://github.com/trustedanalytics/platform-appstack
    https://github.com/trustedanalytics/terraform-aws-intel
    https://github.com/trustedanalytics/uaa-intel-boshrelease
    https://github.com/trustedanalytics/uaa

### AWS Configuration

* You should create an AWS account (or AWS user) that has never been used before. The account/user needs to be capable of manipulating AWS EC2/S3/Route 53 resources.
* **Request region EC2 instance limit increase** via AWS support (default: 20, required as described in [[Pricing Matrix for AWS Deployments]] + 10 more needed for deployment process).

#### Get your AWS keys

If you already know your AWS credentials (access_key_id and secret_access_key, which are not the same as your AWS login and password), you can skip this step.

Start by logging on to AWS: https://console.aws.amazon.com

Click on “Instances” in the left pane and select your deployment region:
![](wikiImages/tf-training-manual18.png)

Select the dropdown next to your login name and select “Security Credentials”:
![](wikiImages/tf-training-manual01.png)

Select _Create New Access Key_.  If there are already keys created, you might want to consult someone in your organization to obtain the access_key_id and secret_access_key, as there is a limit of two sets of Access Keys that can exist.
![](wikiImages/tf-training-manual10.png)

You will be prompted to download the security file as a CSV file.  DO THIS!  You cannot retrieve the AWS secret key once the screen is closed.
![](wikiImages/tf-training-manual12.png)

Document the access_key_id and secret_access_key somewhere privately and securely within your organization and keep these safe. These are the only two pieces of information needed to consume AWS resources by fraudsters.

This example deployment assumes the following keys:
```YAML
    AWS_Access_Key_ID: AKIAJ354GOFUDGEYRMTT
    AWS_Secret_Access_Key: c9/flkkasITUmdlQdzbnIu4ff+UgIOQuB/xb
```
#### Create an SSH Key

If you already have a Key Pair created, you can skip creating a new key pair. You will still need to place a copy of the pem file, as shown below.

Making sure that your deployment region is selected as the AWS Region, click “Key Pairs” then “Create Key Pair”
![](wikiImages/tf-training-manual11.png)

Name your key pair name “bosh” and click “Yes”:
![](wikiImages/tf-training-manual14.png)

After you click “Yes”, a file will be downloaded to your computer and likely named “bosh.pem.txt”. Rename this file to `bosh.pem` and save it into your `~/.ssh` folder on your computer. For example, on OSX you can do this from the terminal:
```bash
    $ mkdir -p ~/.ssh
    $ mv ~/Downloads/bosh.pem.txt ~/.ssh/bosh.pem
```
Change the permissions for the _bosh.pem_ file. For example, on OSX you can do this from the terminal:
```bash
    $ chmod 400 ~/.ssh/bosh.pem
```
### Local configuration

If you have an OSX or Linux computer you are working from locally, there are a couple pieces of software you will need installed:
 - Git
 - Terraform 0.5.3

#### Install and configure Git
You likely already have Git installed if you do any development locally.  Follow the instructions here to install git: http://git-scm.com/book/en/v2/Getting-Started-Installing-Git a short summary is:
 - OSX: `brew install git`
 - RHEL/CentOS/Fedora: `sudo yum install -y git`
 - Ubuntu/Debian: `sudo apt-get install -y git`

When the Git client is ready, configure it

`    $ vi ~/.ssh/config`

>Add following lines:
```ini
    Host github.com
        User                    git
```

Create ssh keypair (the command will create pair of keys in in `~/.ssh` directory)
```bash
    $ ssh-keygen -t rsa
    Generating public/private rsa key pair.
    Enter file in which to save the key (/home/ubuntu/.ssh/id_rsa):
    Enter passphrase (empty for no passphrase):
    Enter same passphrase again:
    Your identification has been saved in /home/ubuntu/.ssh/id_rsa.
    Your public key has been saved in /home/ubuntu/.ssh/id_rsa.pub.
    The key fingerprint is:
    cc:dd:ee:ff:00:11:22:33:44:55:66:77:88:99:aa:bb ubuntu@ip-127-0-0-1
    The key's randomart image is:
    +--[ RSA 2048]----+
    | xyxyxyxE        |
    |  = cc .   o     |
    |   + .occ +      |
    |  . +  X o       |
    |   o =6 S        |
    |    o 7o .       |
    |      =          |
    |                 |
    |                 |
    +-----------------+
```
```bash
    $ ls ~/.ssh/id*
    /home/ubuntu/.ssh/id_rsa  /home/ubuntu/.ssh/id_rsa.pub
```
Login to GitHub account using any browser, then open https://github.com/settings/ssh and **Add SSH key** using content of `~/.ssh/id_rsa.pub`

#### Install Terraform

Visit the following website to download a version of Terraform for you local computer: https://www.terraform.io/downloads.html

We need to use a specific version of Terraform, the latest supported version of Terraform is v0.5.3.  Download Terraform here: https://dl.bintray.com/mitchellh/terraform/terraform_0.5.3_linux_amd64.zip

After you download the appropriate zip, copy the files to a folder in your PATH. The example below uses _~/bin_. 

    $ unzip terraform_0.5.3_linux_amd64.zip -d ~/bin

Open a new terminal window and run:

    $ terraform -v

You should get the following output:

    Terraform v0.5.3

### Deployment configuration

Now that you have your AWS keys, you can supply these values to Terraform to deploy Cloud Foundry.

#### Clone the Repo

Make sure your GitHub account has access to `https://github.com/trustedanalytics/terraform-aws-intel.git` repository. If you don't have access to this repository, next step will fail.

Obtain terraform configuration data
```bash
    $ git clone git@github.com:trustedanalytics/terraform-aws-intel.git
    $ cd terraform-aws-intel
    $ cp terraform.tfvars.example terraform.tfvars
```
During AWS Configuration you obtained AWS keys:
```YAML
    AWS_Access_Key_ID: AKIAJ354GOFUDGEYRMTT
    AWS_Secret_Access_Key: c9/flkkasITUmdlQdzbnIu4ff+UgIOQuB/xb
```
...and you have created an AWS SSH key pem file:
```bash
    ~/.ssh/bosh.pem
```
#### Edit variable file
Generate a secure password for CF admin account, write it down. Using a text editor (in the examples we will use vi) edit the **terraform.tfvars** file.

:warning: Supply **AWS keys**, **AWS SSH keyfile path**, **AWS deployment region**, also set up your future **CF admin password**, as well as appropriate **deployment size**, **CF release** and **CF boshworkspace** versions.
```bash
    $ vi terraform.tfvars
```
After editing, your file should look like this:
```ini
aws_access_key = "AKIAJ354GOFUDGEYRMTT"
aws_secret_key = "c9/flkkasITUmdlQdzbnIu4ff+UgIOQuB/xb"
aws_key_path = "~/.ssh/bosh.pem"
aws_key_name = "bosh"
aws_region = "us-west-1"
network = "10.10"
cf_admin_pass = "{password}"
cf_size = "tiny"
hadoop_worker_count = "3"
hadoop_instance_type = "m3.xlarge"
aws_tags = ""

# Control the version of CF to be deployed
cf_release_version="211"

# Control the version of the CF boshworkspace
cf_boshworkspace_version="master"

# Control the domain for base CF:
cf_domain="XIP" # Use "XIP" for an xip.io based domain, or provide your own domain here

# Prevent Host Header spoofing from hitting internal APIs:
private_cf_domains="xip.internal"

# Control the cloudfoundry deployment size
# Values are 'small', 'med', 'med-ha', and 'big-ha'
# See https://github.com/cloudfoundry-community/terraform-aws-cf-install/blob/master/variables.tf for how
# those sizes inform instance counts
deployment_size="med-ha"

# Should I install docker?
install_docker_services="true"

# Should I install logsearch?
install_logsearch="false"

# Optionally pass user and personal access token for access to github.com
gh_auth="{user:token}"
```

## The Deployment
### Deploy AWS resources
Now you are ready to deploy, run:
```bash
    $ make update
    $ make plan
    $ make apply
```
:information_source: You can use screen or tmux for the task, as its completion takes considerable amount of time.

:information_source: The next step takes considerable amount of time to finish. One can start [Install CDH](Platform Deployment Procedure in AWS#install-cdh) in parallel. 

```bash
    $ make provision
```

It will take about an hour to deploy everything to AWS. Don’t panic if an error occurs during `make apply` or `make provision`, run the command again as AWS resources aren’t always available when requested.

When the installation has completed, your can get some important deployment parameters by parsing `terraform show` output:

```bash
    $ terraform show -no-color -module-depth=2 \
      |awk '/:/{gsub(":",".");pfx=$0} !/:/{gsub("^  ","");print pfx$0}' \
      |awk '/cf_api_id|bastion_ip|cdh-manager\.private_ip/{print $0}'
    module.cloudera.aws_instance.cdh-manager.private_ip = 10.10.10.41
    Outputs.bastion_ip = 52.19.62.177
    Outputs.cf_api_id = 52.19.82.149
```

The obtained values should help you connecting to Cloud Foundry and the Bastion server.

### Install CDH
Run generate_inv.sh script, that will log you into cloudera-launcher, then run ansible.

:information_source: You can use screen or tmux for the task, as its completion takes considerable amount of time.
```bash
    $ eval `ssh-agent`
    $ ssh-add ~/.ssh/bosh.pem
    $ ./cdh/generate_inv.sh
    $ cd ansible-cdh
    $ bash run_ansible.sh
```
This script will ask for accepting Oracle JDK license terms and for the vault password. The password needs to be delivered by the vault owner. 

Currently, the open sourced version requires three changes to work before running `bash run_ansible.sh`. You need to change the following files:
```bash
>>> site.yml
change:
#install ATK client on all hosts
- hosts: cdh-all-nodes
  vars_files:
    - defaults/proxy.yml
  roles:
    - cloudera_atk_client
to:
#install ATK client on all hosts
#- hosts: cdh-all-nodes
#  vars_files:
#    - defaults/proxy.yml
#  roles:
#    - cloudera_atk_client

>>> roles/cloudera_api_manager/tasks/cluster.yml
change:
  cdh: action=create_cluster version={{ cdh_packages_version }} license={{ cdh_license }}
to:
  cdh: action=create_cluster version={{ cdh_packages_version }}

>>> roles/cloudera_api_manager/tasks/main.yml
change:
- name: include gearpump role creation
  include: gearpump.yml
to:
#- name: include gearpump role creation
#  include: gearpump.yml
```
This will stop the ATK client and Gearpump installation. We will fix that in the near future.

:warning: This step takes about 1 hour. After it finishes, check the final output `PLAY RECAP ****` section. 
If the section contains any errors (means there are rows containing `failed` value other than zero, re-run `bash run_ansible.sh`.

:information_source: In order to connect to CDH manager WebUI, you will need to set up port forwarding.
>Every time you need to access the WebUI, you can follow the steps below on your local endpoint computer (needs to have a webbrowser installed). This forwards CDH manager WebUI port to `http://localhost:7180/`, as long as the ssh session stays connected. 
```bash
    # uncomment if ssh-agent is not yet ready
    $ #eval `ssh-agent`
    $ #ssh-add ~/.ssh/bosh.pem
    $ ./cdh/generate_inv.sh
```

### Set up bosh deployment

From your local machine log in to box shown as bastion_ip
```bash
    $ ssh -i ~/.ssh/bosh.pem ubuntu@bastion_ip
```

Make sure that you have latest gems
```bash
    $ cd ~/workspace/deployments/cf-boshworkspace/
    $ bundle update
```

Create two template files and one credentials file

`    $ vi ~/workspace/deployments/cf-boshworkspace/templates/cf-project-login.yml`
>```YAML
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

`    $ vi ~/workspace/deployments/cf-boshworkspace/templates/cf-project-login-additional-properties.yml`
>```YAML
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

`    $ vi ~/workspace/deployments/cf-boshworkspace/.credentials.yml`
>```YAML
- url: https://github.com/trustedanalytics/uaa-intel-boshrelease.git
  username: YOUR_GITHUB_USERNAME
  password: YOUR_GITHUB_PERSONAL_ACCESS_TOKEN
```

Edit template

`    $ vi ~/workspace/deployments/cf-boshworkspace/templates/tiny/cf-tiny-scalable.yml`
>Locate path `meta -> services_templates` and replace:
```YAML
...
meta:
...
  services_templates:
...
  - name: uaa
    release: (( meta.release.cf ))
```
with:
```YAML
meta:
...
  services_templates:
...
  - name: uaa-intel
    release: uaa-intel
```

Edit deployment file

`    $ vi ~/workspace/deployments/cf-boshworkspace/deployments/cf-aws-tiny.yml`

>Locate path `releases` and append the following:
```YAML
  - name: uaa-intel
    version: 1
    git: https://github.com/trustedanalytics/uaa-intel-boshrelease.git
```
Locate path `templates` and modify it:
```YAML
templates:
  - cf/cf-deployment.yml
  - tiny/cf-tiny-scalable.yml
  - cf-uaa-port.yml
  - cf-allow-services-access.yml
  # add the below line
  - cf-project-login-additional-properties.yml
  - cf/cf-properties.yml
  - cf/cf-infrastructure-aws.yml
  - cf-properties.yml
  - parallel.yml
  - cf-no-ssl.yml
  - cf-secrets.yml
  - tiny/cf-use-nfs.yml
  - tiny/cf-use-haproxy.yml
  - cf-networking.yml
  # add the below line
  - cf-project-login.yml
```
Locate path `meta` and append the following:
```YAML
  login_smtp:
    host: mail.YOURSMTP.com
    port: 25
    user: YOURUSER
    password: YOURPASS
    senderEmail: support@domain.com
    senderName: Domain
```
Locate path `meta -> instances` and increase number of `runner_z1` VMs to **3**
```YAML
...
meta:
...
  instances:
...
    runner_z1:    3 # MARKER_FOR_PROVISION
...
```
Generate a secure password for uaac_admin_client_secret. 
Locate path `meta` and set generated password to `secret`.
```YAML
...
meta:
...
  secret: <uaac_admin_client_secret>
...
```

Edit template

`    $ vi ~/workspace/deployments/cf-boshworkspace/templates/cf-properties.yml`

>Locate path `properties -> dea_next` and increase number of `disk_mb` to **860000**
```YAML
...
properties:
...
  dea_next:
...
    disk_mb: 860000
...   
```

Deploy changes
```bash
    $ cd ~/workspace/deployments/cf-boshworkspace/
    $ bosh deployment cf-aws-tiny
    $ bosh prepare deployment
    $ bosh deploy
```

### Set up Cloud Foundry
Login to Cloud Foundry using admin account and create organization and space
```bash
    $ cf login -a http://api.<cf_api_id>.xip.io/ -u admin -p <cf_admin_pass>
    $ cf create-org <org_name>
    $ cf target -o <org_name>
    $ cf create-space <space_name>
    $ cf target -s <space_name>
```

#### Update security group
Create a file with updated rules for `public_networks` security group to allow connections to 10.0.0.0/8 network:

`   $ vi public_networks.json`
>Copy and paste those rules into the file:
```json
    [
            {
                    "destination": "0.0.0.0-9.255.255.255",
                    "protocol": "all"
            },
            {
                    "destination": "10.0.0.0-10.255.255.255",
                    "protocol": "all"
            },
            {
                    "destination": "11.0.0.0-169.253.255.255",
                    "protocol": "all"
            },
            {
                    "destination": "169.255.0.0-172.15.255.255",
                    "protocol": "all"
            },
            {
                    "destination": "172.32.0.0-192.167.255.255",
                    "protocol": "all"
            },
            {
                    "destination": "192.169.0.0-255.255.255.255",
                    "protocol": "all"
            }
    ]
```

Save the file and update the security group
```bash
    $ cf update-security-group public_networks public_networks.json
```
#### Update default quota
Update the maximum amount of memory to 30 GB:
```bash
    $ cf update-quota default -m 30G
```

#### Add UAA clients
```bash
    $ uaac target https://uaa.<cf_api_id>.xip.io/ --skip-ssl-validation
    $ uaac token client get admin -s <uaac_admin_client_secret>
    $ uaac client add atk-client \
      --scope cloud_controller.read,cloud_controller.write,cloud_controller_service_permissions.read,openid \
      --authorized_grant_types authorization_code,password,refresh_token \
      --redirect_uri http://atkdash.<cf_api_id>.xip.io/managed/auth/cloudfoundry/callback \
      --authorities uaa.none -s <uaac_admin_client_secret>
    $ uaac client update developer_console \
      --scope cloud_controller.admin,cloud_controller.read,cloud_controller.write,console.admin,doppler.firehose,openid,password.write,scim.read,scim.userids,scim.write \
      --authorized_grant_types authorization_code,client_credentials,refresh_token \
      --redirect_uri http://console.<cf_api_id>.xip.io/oauth/callback,https://console.<cf_api_id>.xip.io/oauth/callback \
      --authorities scim.read,uaa.admin,cloud_controller.admin,billing.admin,uaa.resource,password.write,scim.write,cloud_controller.write,cloud_controller.read \
      --autoapprove true --access_token_validity 1209600 --refresh_token_validity 1209600
    $ uaac group add console.admin
    $ uaac member add console.admin admin
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
Enter your environment information to secret.mk. Edit cloudfoundry api endpoint, user,password, org & space.

Open appstack.mk and change artifact_pfx to the store url. Uncomment proxy and noproxy and put correct proxy values there.

Last file to edit is `settings.yml`. Complete missing values using your environment information.

NOTE:
Generate HADOOP_PROVIDED_PARAMS using this instruction: `https://github.com/trustedanalytics/hdfs-broker#injection-of-hdfs-client-configuration`.

Now you are ready to deploy whole platform:
```bash
make deploy
```
** Make sure you are on intel network, since it picks apps from intel nexus repository and repository is behind the firewall. appstack.mk (add proper proxy, & no proxy setting)
# Devops remarks

* Set up and assign to AWS user an appropriate account regional policy, i.e. (please remember to change the `ec2:Region` attribute):

```JSON5
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ec2:*",
                "vpc:*"
            ],
            "Condition": {
                "StringEquals": {
                    "ec2:Region": "us-east-1"
                }
            },
            "Resource": [
                "*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "elasticloadbalancing:*"
            ],
            "Resource": [
                "*"
            ]
        }
    ]
}
```
