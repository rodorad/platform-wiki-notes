> [Wiki](Home) ▸ **Platform Deployment in AWS**

## Prerequisites

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

For the first time please follow instructions from here: 

https://github.com/trustedanalytics/platform-wiki/wiki/Platform-Deployment-Procedure-local-setup

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
Generate a secure password for CF admin account, write it down. Using a text editor (in the examples we will use vi) edit the **terraform.tfvars** file. Fill **AWS keys**, **AWS SSH keyfile path**, **AWS deployment region**, also set up your future **CF admin password**, as well as appropriate **deployment size**. Note that cf_admin_pass should not contain special characters due to the limitations of CloudFoundry, please use characters [0-9A-Za-z].

```bash
    $ vi terraform.tfvars
```
After editing, your file should look like this:
```ini
# Change needed
aws_access_key = "AKIAJ354GOFUDGEYRMTT"
aws_secret_key = "c9/flkkasITUmdlQdzbnIu4ff+UgIOQuB/xb"
cf_admin_pass = "MY-PASSWORD"

# Default values, may be changed if needed

aws_key_path = "~/.ssh/bosh.pem"
aws_key_name = "bosh"
aws_region = "us-west-1"
network = "10.10"
cf_size = "tiny"
hadoop_worker_count = "3"
hadoop_instance_type = "m3.xlarge"

# tag values, key order: Project,IAP,Environment
aws_tags = "Foo Bar,00000,Development"
cf_domain="XIP" # Use "XIP" for an xip.io based domain, or provide your own domain here

# Prevent Host Header spoofing from hitting internal APIs:
private_cf_domains="xip.internal"

# Control the cloudfoundry deployment size
# Values are 'small', 'med', 'med-ha', and 'big-ha'
# See https://github.com/cloudfoundry-community/terraform-aws-cf-install/blob/master/variables.tf for how
# those sizes inform instance counts

deployment_size="small"
install_docker_services="true"
install_logsearch="false"

# Expert only! Do not change if unsure

# Control the version of CF to be deployed
cf_release_version="211"

# Control the version of the CF boshworkspace
cf_boshworkspace_version="master"
```

## The Deployment
### Deploy AWS resources

:warning: Make sure your local computer's system time is set correctly, or the process will fail.

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

When the installation has completed, you will get few values on output:
```bash
Example:
module.cloudera.aws_instance.cdh-manager.private_ip = 10.10.10.41
Outputs.bastion_ip = 52.19.62.177
Outputs.cf_api_id = 52.19.82.149
```
The obtained values should help you connecting to Cloud Foundry and the Bastion server.

### Install CDH
Run generate_inv.sh script, that will log you into cloudera-launcher, then run ansible.

:information_source: You can use screen or tmux for the task, as its completion takes considerable amount of time.
```bash
    $ ./cdh/generate_inv.sh
    $ cd ansible-cdh
    $ bash run_ansible.sh
```
This script will ask for accepting Oracle JDK license terms and for the vault password. The password needs to be delivered by the vault owner. 

:warning: This step takes about 1 hour. After it finishes, check the final output `PLAY RECAP ****` section. 
If the section contains any errors (means there are rows containing `failed` value other than zero, re-run `bash run_ansible.sh`.

:information_source: In order to connect to CDH manager WebUI, you will need to set up port forwarding.
>Every time you need to access the WebUI, you can follow the steps below on your local endpoint computer (needs to have a webbrowser installed). This forwards CDH manager WebUI port to `http://localhost:7180/`, as long as the ssh session stays connected. 
```bash
    $ ./cdh/generate_inv.sh
```

### Set up bosh deployment

Follow the instructions from this document: 

https://github.com/trustedanalytics/platform-wiki/wiki/Platform-Deployment-Procedure:-bosh-deployment
