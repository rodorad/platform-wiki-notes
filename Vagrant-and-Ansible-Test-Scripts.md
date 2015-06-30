## Using Vagrant

1. Download Vagrant:

    ```
    wget https://dl.bintray.com/mitchellh/vagrant/vagrant_1.7.2_x86_64.deb
    ```

9. Install Vagrant with this command:

    ```
    sudo dpkg -i vagrant_1.7.2_x86_64.deb`
    ```

9. Do the following only on Intel machines, when you need to have a proxy:
    ```
    sudo apt-get install libffi-dev
    sudo apt-get install build-essential
    vagrant plugin install vagrant-proxyconf
    ```

9. Install the plugin for LXC: 

    ```
    vagrant plugin install vagrant-lxc`
    ```

9. Install LXC itself: 

    ```
    sudo apt-get install lxc`
    ```

## Using Ansible

1. Install Ansible: 

    ```
    sudo apt-add-repository ppa:ansible/ansible
    sudo apt-get update -qq
    sudo apt-get install ansible
    ```
9. Clone the `platform-ansible` repository

## Example test
* Make sure no machines with the role are up: `vagrant destroy zabbix-server`
* Bring the machine with a specified role up: `vagrant up zabbix-server`
* When changing scripts, reprovision the machine: `vagrant provision zabbix-server`
* To debug the machine: `vagrant ssh zabbix-server`
