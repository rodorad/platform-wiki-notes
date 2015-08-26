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

Edit deployment file
* for AWS deployments: 
```    $ vi ~/workspace/deployments/cf-boshworkspace/deployments/cf-aws-tiny.yml```
* for OpenStack deployments: 
```    $ vi ~/workspace/deployments/cf-boshworkspace/deployments/cf-openstack-tiny.yml```

Locate path `meta` and fill the following section with your own values:
```YAML
  login_smtp:
    host: mail.YOURSMTP.com
    port: 25
    user: YOURUSER
    password: YOURPASS
    senderEmail: support@domain.com
    senderName: Domain
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

Deploy changes

```bash
    $ cd ~/workspace/deployments/cf-boshworkspace/
```
* for AWS deployments: 
```bash
    $ bosh deployment cf-aws-tiny
```
* for OpenStack deployments: 
```bash
    $ bosh deployment cf-openstack-tiny
```

and execute: 
```bash
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
