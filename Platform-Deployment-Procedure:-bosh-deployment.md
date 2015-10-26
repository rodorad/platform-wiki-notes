### Set up bosh deployment

From your local machine log in to box shown as bastion_ip
```bash
    $ ssh -i ~/.ssh/bosh.pem ubuntu@bastion_ip
```
NOTE: _**this command is incorrect it should. The user needs to use the pem file they used for jump_box_key path. Make it obvious that bastion_ip is not a variable and only a placeholder**_

Make sure that you have latest gems
```bash
    $ cd ~/workspace/deployments/cf-boshworkspace/
    $ bundle update
```
NOTE: _**The directory doesn't exist**_

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
NOTE: _**explain consequences if mail is not configured and how to configure it later**_

Generate a secure password for uaac_admin_client_secret. 
Locate path `meta` and set generated password to `secret`. Note that this password should not contain special characters due to the limitations of CloudFoundry, please use characters [0-9A-Za-z].
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
    $ cf login -a http://api.<cf_api_id>.xip.io/ -u admin -p <cf_admin_pass> --skip-ssl-validation
    $ cf create-org <org_name>
    $ cf target -o <org_name>
    $ cf create-space <space_name>
    $ cf target -s <space_name>
```
NOTE: _**cf_api_id doesn't exist. if it does where can i get it? The only close to this is the cf_api. For my installtion cf_api=api.run.10.54.8.57.xip.io i'm sure this is the value but the doc needs to be updated. cf_api value is close enought to confuse you and is also wrong becuase .run is no longer used in the url**_

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
      --authorized_grant_types authorization_code,password,refresh_token,client_credentials  \
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
    $ uaac client update cf \
      --scope cloud_controller.admin,cloud_controller.read,cloud_controller.write,doppler.firehose,openid,password.write,scim.read,scim.write,console.admin
```

NOTE:_**When ever possible we should avoid user side compilation. The first time setup of a development environment is quite laborous and should be avoided by customers when ever possible.**_
## Push apps and services
Follow the instructions in [Platform Parent](https://github.com/trustedanalytics/platform-parent) readme.

NOTE: _**This part of the installation was ignored and instead of compiling packages they were downloaded from nexus directly. This breaks The entire installation instructions from here on down**_

NOTE: _**smtp service was missing the  port_ssl configurations**_

NOTE: _**The user_management service needed to have it's timeout increased to 180 seconds to accomodate the slowness of openstack.**_

Next, clone two repos:
```bash
git@github.com:trustedanalytics/cloudfoundry-mkappstack.git
git@github.com:trustedanalytics/platform-appstack.git
```

Next step is filling the data in Cloud Foundry platform definition files, which provide necessary data for deployment process.

Please, follow instructions in [Platform Appstack](https://github.com/trustedanalytics/platform-appstack) 


Copy files from platform-appstack to cloudfoundry-mkappstack:
```bash
cp platform-appstack/appstack.yml cloudfoundry-mkappstack/appstack.yml
cp platform-appstack/settings.yml cloudfoundry-mkappstack/settings.yml
```
Go to cloudfoundry-mkappstack directory and copy templates
```bash
cd cloudfoundry-mkappstack
cp appstack.mk.tmpl appstack.mk
cp secret.mk.tmpl secret.mk
```

NOTE: _**deployments/docker-openstack.yml doesn't get the correct admin secret specified in workspace/deployments/cf-boshworkspace/deployments/cf-openstack-tiny.yml**_


Enter your environment information to secret.mk. Edit cloudfoundry api endpoint, user,password, org & space.

Open appstack.mk and change artifact_pfx to the store url. Uncomment proxy and noproxy and put correct proxy values there.

Now you are ready to deploy whole platform:
```bash
make deploy
```
NOTE: _**While i'm not sure how to recreate the problem i tried several times to run make deploy and got this error serveral times**_
```
ubuntu@bastion:~/workspace/tools/cloudfoundry-mkappstack$ make deploy
[upenn] -{ok}-[cli] authenticated: User:admin API:http://api.10.54.8.57.xip.io Org:tap Spac                                                                                                                                          e:upenn Space GUID:7e440cde-f1a6-4b52-8a2f-9bf3e0b9da83
lib/ruby/cfspace2mft.rb:52:in `<main>': undefined method `map' for nil:NilClass (NoMethodEr                                                                                                                                          ror)
rm upenn_upsi.json upenn_services.json sbks/docker/sbkrmt_mfst.yml upenn_upsi.yml upsi/hive                                                                                                                                          /upsrmt_mfst.yml upsi/gorouter-provider/upsrmt_mfst.yml upenn_summary.json upsi/kerberos-se                                                                                                                                          rvice/upsrmt_mfst.yml upsi/sso/upsrmt_mfst.yml upsi/nats-provider/upsrmt_mfst.yml upenn_sum                                                                                                                                          mary.yml upenn_sbrokers.json upenn_sbrokers.yml upsi/external-tools/upsrmt_mfst.yml

```

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
