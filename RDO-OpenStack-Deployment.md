## Hardware setup
* 1x Controller and storage node: 4 CPUs with 6 cores, 48 GB of RAM, 128GB for / filesystem, 2 TB RAID1 for cinder-volumes volume group, 2 NICs (either 1 Gbps or 10 Gbps).
* 6x Compute node (each): 4 CPUs with 4 cores, 48 GB RAM, 256GB for / filesystem, 2 NICs (either 1 Gbps or 10 Gbps).
* Screen, keyboard.

## Initial setup 
In our case for networking GRE tunneling was used because it is relatively easy to setup, does not need any external api aware switches with vlan support and in theory scales flexibly.
On every node 1st nic (with a private IP) is used for general intra-cluster communication and networking tunnels (GRE). 2nd nic (with a public IP) is used for external connectivity. Usually floating ip addresses are added from the subnet assigned to the 2nd nic to virtual machines which need external connectivity (NAT translation).
Good document which can bring some light into OpenStack networking can be found at: https://www.hastexo.com/system/files/neutron_packet_flows-notes-handout.pdf.

## Preparing OpenStack nodes
In case of RDO distribution a tool based on puppet called packstack is being used for installation and configuration management of the cluster. One node should be choosen as the packstack master, from it all configuration commands will be invoked.
Prerequisites:
* minimal installation of CentOS 7 x86_64 on all nodes, updated to newest version of packages after installation
* Network Manager service should be stopped and disabled
* RDO repository present on all nodes and enabled in the os
$ sudo yum install -y https://rdoproject.org/repos/rdo-release.rpm
* packstack installed on master
$ sudo yum install -y openstack-packstack
* ssh passwordless access between packstack master and other hosts should be enabled
* iptables service should be enabled and running but all rules from all tables and chains should be removed, all nonstandard chains should be also deleted
* hostname should be set to full qualified domain name so that below command returns fqdn
$ hostname
* ntpd should be enabled and running to avoid hard to debug problems in the future
* on controller/storage node a volume group called cinder-volumes should be created for volumes which will be exported by iscsi (default) to spawned virtual machines in cinder service

Optionally:
* SELinux can be disabled because it adds some performance penalty

From experience based on RDO OpenStack Kilo:
* on controller node php-5.4 should be installed before starting the installation, otherwise horizon will not start properly
* on compute nodes below packages should be installed, libvirtd service should be enabled and running, otherwise nova will not start properly
$ yum -y install qemu-kvm qemu-img virt-manager libvirt libvirt-python python-virtinst libvirt-client virt-install virt-viewer

## OpenStack installation
* on RDO quickstart page https://www.rdoproject.org/Quickstart there is a suggestion to use packstack in all-in-one mode but from our experience it is better to use it to generate answer file first, then edit it and run installation based on the modified file.
$ sudo packstack --gen-answer-file packstack-answers.txt
* values to modify
 * CONFIG_SWIFT_INSTALL=n
 * CONFIG_CEILOMETER_INSTALL=n
 * CONFIG_HEAT_INSTALL=n
 * CONFIG_NAGIOS_INSTALL=n
 * CONFIG_NOVA_COMPUTE_HOSTS= -> ip addresses for all nova compute nodes should be added here
 * CONFIG_NEUTRON_OVS_TENANT_NETWORK_TYPE=gre
 * CONFIG_NEUTRON_OVS_TUNNEL_RANGES=1000:3000
 * CONFIG_NEUTRON_OVS_TUNNEL_IF= -> interface name for private network interface
 * CONFIG_PROVISION_DEMO=n -> we do not need any demo tenants created
 * CONFIG_CINDER_VOLUMES_CREATE=n -> it is better to use previously created volume group on storage node

$ sudo packstack --answer-file=modified_packstack-answers.txt
* after successful installation an authorization file will be created by default in ~/root/keystone_admin, it should be sourced before commandline usage of any openstack shell command.

## Testing OpenStack installation
After succesfull installation it can be checked by uploading a lightweight os image which is used to spawn a virtual machine. Good example is cirros image available at https://download.cirros-cloud.net/.

## Cluster preparation for Trusted Analytics deployment
* for every Trusted Analytics deployment a dedicated tenant should be added
* for every new tenant two users should be created, first with member privileges (it will be used during installation), second with admin privileges (it will be used for general maintenance of OpenStack tenant)