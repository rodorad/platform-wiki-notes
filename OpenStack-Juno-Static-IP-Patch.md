
There is a [bug](https://review.openstack.org/#/c/149905/2/nova/network/neutronv2/api.py) in OpenStack Juno that prevents BOSH from assigning static IPs.  This can be quite an issue when trying to install Cloud Foundry on [OpenStack](https://github.com/cloudfoundry-community/terraform-openstack-cf-install).

This topic explains a very simple fix.

## Log on to the controller node

From the Mirantis Fuel Dashboard, navigate to _Nodes > Controller_ and click on the gear icon.  The FQDN will list the server you will need to log on to.  For this example, _ssh_ into the _node-100.prod.myorg.com_ server:

```bash
ssh node-100
```

Then assign all the environment variables:
```bash
source openrc
```

To find the list of compute nodes, run the following:
```bash
nova service-list
```

The table below shows an example output. (Some columns were removed for easier reading.)
```
+----+------------------+-------------------------+---------+
| Id | Binary           | Host                    | Status  |
+----+------------------+-------------------------+---------+
| 1  | nova-consoleauth | node-100.prod.myorg.com | enabled |
| 2  | nova-scheduler   | node-100.prod.myorg.com | enabled |
| 3  | nova-conductor   | node-100.prod.myorg.com | enabled |
| 4  | nova-cert        | node-100.prod.myorg.com | enabled |
| 5  | nova-compute     | node-105.prod.myorg.com | enabled |
| 6  | nova-compute     | node-106.prod.myorg.com | enabled |
| 7  | nova-compute     | node-107.prod.myorg.com | enabled |
| 8  | nova-compute     | node-108.prod.myorg.com | enabled |
| 9  | nova-compute     | node-109.prod.myorg.com | enabled |
+----+------------------+-------------------------+---------+
```

## Fix the first compute node

Referring to the output table above, _ssh_ into the first compute node, _node-105_: 
```
ssh node-105
```

Once on this server, open the _api.py_ file:
```
vi /usr/lib/python2.6/site-packages/nova/network/neutronv2/api.py
```

Replace the following line:
```bash
port_req_body['port']['fixed_ips'] = [{'ip_address': fixed_ip}]
```
...with:
```bash
port_req_body['port']['fixed_ips'] = [{'ip_address': str(fixed_ip)}]
```

Save the file and restart nova-compute:
```bash
service openstack-nova-compute restart
```


## Fix the remaining compute nodes

You can repeat the previous section for the remaining compute nodes (node-106, node-107, node-108, and node-109), or leverage the first server fixed, copy the _api.py_ file, and restart nova-compute.

To leverage the first compute server's patch, get the list of remaining compute servers you need to patch.
When you ran _nova service-list_ on the controller node, there were five compute nodes listed, starting with _node-105_ and ending with _node-109_. You've already done _node-105_ so you still need to patch _node-106_ through _node-109_.

```bash
for i in {106..109}
do
   scp node-106:/usr/lib/python2.6/site-packages/nova/network/neutronv2/api.py node-$i:/usr/lib/python2.6/site-packages/nova/network/neutronv2/api.py
   ssh node-$i service openstack-nova-compute restart
done
```

Now you can deploy Cloud Foundry and use static IPs.
