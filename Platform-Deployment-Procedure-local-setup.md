## Local setup
### GitHub Access

You should be capable to clone several GitHub repositories. If any of the repositories is private, you need to have a GitHub account, that is capable to access following repositories:

    https://github.com/trustedanalytics/cloudfoundry-mkappstack
    https://github.com/trustedanalytics/docker-services-boshworkspace
    https://github.com/trustedanalytics/hdfs-broker
    https://github.com/trustedanalytics/platform-appstack
    https://github.com/trustedanalytics/terraform-aws-intel
    https://github.com/trustedanalytics/uaa-intel-boshrelease
    https://github.com/trustedanalytics/uaa

### Local configuration

If you have an OSX or Linux computer you are working from locally, there are a couple pieces of software you will need installed:
 - Git
 - Terraform 0.5.3

#### Install and configure Git
You likely already have Git installed if you do any development locally.  Follow the instructions here to install git: http://git-scm.com/book/en/v2/Getting-Started-Installing-Git a short summary is:
 - OSX: `brew install git`
 - RHEL/CentOS/Fedora: `sudo yum install -y git`
 - Ubuntu/Debian: `sudo apt-get install -y git`
NOTE: _**the default git version(1.7.1) for centos doesn't work.**_ 
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

### Important

If you have problem with reaching github through ssh protocol, 
create your local ~/.gitconfig file with content:
```
[url "https://"]
    insteadOf = git://
[url “https://github.com/“] 
    insteadOf = git@github.com:

```
