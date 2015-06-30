## Operating system

Install Ubuntu* operating system (version 14.04 or later).

> **General note:** If any package that is a prerequisite to install other packages listed in this document is not installed on your system (you will be informed which needed packages are missing in the console output), install it with:

   ```
sudo apt-get install <package_name>
```

## Cloud Foundry command line interface

1. Download CF CLI version 6.11.1 with the following:

    ```
wget http://go-cli.s3-website-us-east-1.amazonaws.com/releases/v6.11.1/cf-cli_<version>.deb
```

     ...where _<version>_ should be replaced with _amd64_ or _i386_, depending on your operating system bitness.

9. For Mac OS X*, use the following:

    ```
wget http://go-cli.s3-website-us-east-1.amazonaws.com/releases/v6.11.1/cf-darwin-amd64.tgz
```

9. Install the downloaded package:

    ```
sudo dpkg -i cf-cli_<version>.deb
```

## Developer tools

### Java

Add the following repositories:

* sudo add-apt-repository ppa:chris-lea/node.js
* sudo add-apt-repository ppa:webupd8team/java

**Note:** If the console output displays "sudo: add-apt-repository: command not found" while trying to add repositories, then install the following packages with this command:

```
sudo apt-get install software-properties-common python-software-properties
```

Update package lists from repositories:

```
sudo apt-get update
```

> **Note:** If you do not update package lists after adding _ppa:webupd8team/java_,  you will not be able to install Java 8 ("Unable to locate package oracle-java8-installer").

Install the following packages with these commands:

```
sudo apt-get install oracle-java8-installer oracle-java8-set-default
```

### Maven 
(New to Maven? https://maven.apache.org/guides/getting-started/)

```
sudo apt-get install maven
```

### Node.js

```
sudo apt-get install nodejs
```

### Python

```
sudo apt-get install python python-dev python-setuptools python-pip
```

Install the following packages using _pip_:

```
sudo pip install virtualenv
sudo pip install tox
```

### Git

    sudo apt-get install git
    
### Foreign Function Interface Library

    sudo apt-get install libffi-dev 
  

### Go

1. Install bison:

    ```
    sudo apt-get install bison
    ```

9. Install gvm (Go Version Manager):

    ```
    bash < <(curl -s -S -L https://raw.githubusercontent.com/moovweb/gvm/master/binscripts/gvm-installer)
    ```

     If you are using zsh, replace _bash_ with _zsh_.

9. Install Go version 1.4.2 and use it by default:

    ```
    gvm install go1.4.2
    gvm use go1.4.2
    ```

9. If you encounter a compilation error, you should update your git client to the latest version.

9. Install gccgo-go

    ```
    sudo apt-get install gccgo-go
    ```

9. Setup Go workspace:

    ```
    mkdir ~/go-workspace
    export GOPATH=$HOME/go-workspace
    export PATH=$PATH:$GOPATH/bin
    ```
    
    Add the above two exports to your ~/.bashrc file if you want to have these variables exported permanently.

9. Install Godep

    ```
    go get github.com/tools/godep
    ```

### Ruby

9. Download GPG signature:

    ```
    gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3
    ```

9. Install rvm (Ruby Version Manager):

    ```
    curl -L https://get.rvm.io | bash -s stable
    ```

9. Execute:

    ```
    source ~/.rvm/scripts/rvm
    ```

9. Install Ruby version 2.1.5 and use it by default:

    ```
    rvm install ruby-2.1.5
    rvm use 2.1.5
    ```

9. You might also want to generate Ruby documentation:

    ```
    rvm docs generate-ri
    ```

9. Install gems:

    ```
    gem install compass
    gem install cf-uaac
    ```

Your system is now prepared to clone the repositories of your choice and start development. 
    