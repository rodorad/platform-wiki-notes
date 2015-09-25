# Hardware setup
-	**1x Controller node:** 2 CPUs with 6 cores, 24 GB of RAM, 1 TB RAID1, 4 NICs (either 1 Gbps or 10 Gbps).
-	**1x Storage node:** 1 CPU with 4 physical cores, 12 GB of RAM, 3 TB of ceph storage, 500 GB RAID1, 2 NICs (either 1 Gbps or 10 Gbps).
-	**1x fuel server:** Quad-core CPU, 4 GB RAM, 1 Gbps Ethernet, 128 GB SAS Disk, IPMI access through independent management network.
-	**6x compute node (each):** Dual-socket CPU with at least 4 physical cores per socket, 64 GB RAM, 256 SSD , 4 NICs (either 1 Gbps or 10 Gbps).
-	Screen, keyboard.

For GRE setups 4 NICs per machine are required.

# Initial setup
-	Download the latest version of Mirantis distribution from Mirantis website and make a bootable image on a USB drive using Xfburn for Linux* or ImgBurn for Windows*.
-	Be sure that your network is configured properly: 4 broadcast address, 4 gateways.
-	In fuel node, set up booting up from the USB drive and save the options.
-	When you see the GRUB installer, press the Tab key. Provide network parameters and change _showmenu=no_ to _showmenu=yes_.
-	When the menu appears, pick the options for your installation, save, and quit setup.
This will allow you access to a horizon node via IP provided at the welcome screen. The default username/password is admin/admin.

## Preparing OpenStack nodes
Once the fuel server is up and running, you are ready to deploy OpenStack to nodes. You have to manually set in BIOS PXE boot for all the nodes you will use in your installation. Steps for all nodes:
-	Pin in monitor/keyboard to node.
-	Click F2, F12 or DEL key to enter bios (depends on BIOS).
-	Check boot priority options; if they are not setup for PXE boot (network boot), you should do it.
-	Save BIOS settings.
-	Now wait until PXE boot ends all work: do not hit any key or power off machine; let the process end automatically. 
-	Repeat for all nodes.

## Setting up OpenStack
Log on to your fuel UI. (If you didn’t change your password, it is admin/admin by default.) You will find there button New OpenStack Environment. 
-	**Name and release:** Choose a name for your OpenStack instance, choose Juno on Centos release
-	Choose deployment mode: HA or w/o HA
-	**In compute:** Choose KVM
-	**In networking:** Neutron with GRE, however you can use VLAN segmentation if you have it prepared correctly
-	**Storage backends:** Leave it default
-	Skip to finish (don’t choose any other options)
Now, you are ready to add nodes to your OpenStack configuration. 
Click the green button in the right corner, choose controller node and set all controllers you are using. Repeat for compute and storage nodes.
Now choose nodes 1 by 1 and configure interfaces. All interfaces should be configured correctly – depends on network configuration. Example:
```
ETH0 – Management
ETH1 – Public
ETH2 – Storage
ETH3 – Admin (PXE)
```
## Networks tab – Neutron with GRE segmentation
Set all IP ranges for all networks. Floating IP range should have a minimum of 20 addresses to work well, taken from public network IP’s. Verify networks and save..
### Settings tab
On the bottom of this tab you will find Upstream DNS and Upstream NTP. You have to manually set to your DNS server and NTP server. Be sure that the DNS server is responding null for AAAA network or you will be have problems later on.

If you did all steps, you can now deploy your OpenStack cloud to nodes. It will take 30-120 minutes depends on how big your instance is and how your network performs. 
## Manual steps 
### Volume attaching fix - ``` sysfsutils ```
You can see compute nodes names in fuel UI.
Login on every compute node you have in your installation and install sysfsutils on every of the nodes.
```
sudo su –
```
provide password – it should be r00tme if you didn’t changed it
Centos installation: ``` yum install sysfsutils ```
Ubuntu installation: ``` apt-get install sysfsutils ```
### Floating IP’s assign for bosh vms
Use this wiki page to fix all issues with floating IP:
https://github.com/trustedanalytics/platform-wiki/wiki/OpenStack-Juno-Static-IP-Patch
### Cinder volumes
Login on storage node. IP can be found in fuel UI. 
Use ``` vi /etc/cinder/cinder.conf ```
Find line ``` lvm_type ``` line and change it to ``` thin ```
Uncomment ``` volume_clear ``` and set to ``` none ```.

Use command: ``` /etc/init.d/openstack-cinder-* restart ``` where * is name of all services.
### Mysql max_connections fix for volumes
Login at controller node. Use command
``` set global max_connections = 20000; ```
## Testing OpenStack
You did deployment and manual steps so you’re ready now to test your OpenStack instance.
Login into fuel UI, click on your cloud deployment, you will find Health Check tab out there. Perform sanity and functional tests. 
Sanity tests should be all green, the Functional test should be all green besides network connectivity if you’re using proxy. 

If you made all the steps your OpenStack is ready to use. You can find Horizon UI on the top of fuel UI in message box.
