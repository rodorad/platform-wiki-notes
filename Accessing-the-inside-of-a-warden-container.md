This document explains how to get inside a warden container that runs a given application.

The following is assumed:
- `jq` is installed on all of the machines we will be using (it's as simple as `apt-get install jq` if you don't have it).
- `alamakota` is the administrator password.
- `butla-rest-demo.example.com` is the url the application is using.

### Jumpbox
Connect to the jumpbox and change the current directory to `~/workspace/deployments/cf-boshworkspace`.

### Gorouter IP
Find the IP address of the machine with the gorouter: `bosh vms | awk -F\| '/api/{ print $5 }'`. 

If there are multiple, either one is fine. For this guide, I will assume `10.10.3.7`.

### Runner IP
Get the IP address of the runner that hosts the application: `curl -s http://router:alamakota@10.10.3.7:8080/routes | jq '.["butla-rest-demo.example.com"][]' | sed 's/:.*"/"/'`. 

Remember to replace **alamakota** with the admin password, **butla-rest-demo.example.com** with the application URL and **10.10.3.7** with the API address from the **Gorouter IP** step!

### Runner name
Get the name of the runner that has the IP address we got in the previous step: `bosh vms | awk -F\| '/10.10.3.54/{ print $2 }'`.

Remember to replace **10.10.3.54** with the address from the **Runner name** step!

### Runner SSH
SSH to the runner machine with `bosh ssh runner_z1/3`.

Remember to replace **runner_z1/3** with the name you got from the **Runner name** step!

### Get container ID
Get the container ID running `sudo jq '.instances[] | select(.application_uris[0] == "butla-rest-demo.example.com") | .warden_handle' /var/vcap/data/dea_next/db/instances.json`.

Remember to replace **butla-rest-demo.example.com** with your application URL!

### Jump to container
Jump to the app container with the following command: `/var/vcap/packages/warden/warden/src/wsh/wsh --user vcap --socket /var/vcap/data/warden/depot/18ekh2ovjh5/run/wshd.sock`.

Remember to replace **18ekh2ovjh5** with the output of the **Get container ID** step!

Congratulations! You are in the container!