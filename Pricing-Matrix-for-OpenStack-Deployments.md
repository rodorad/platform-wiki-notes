The following breakdowns are only suggestions.  Please note:
 - Assumes Cloud Foundry apps are 1 GB in size, with low CPU and disk utilization.
 - The workload will ultimately determine the horizontal and vertical sizing.
 - Docker Services should **never** be run in Production mode. 
 - Sizing for CDH environments must be confirmed.

<br>
##### Without HA, 10 apps (Smallest possible)
| Options                  | QTY | RAM (GB) | CPU |
|--------------------------|-----|---------|-----|
| **Cloud Foundry**        |
|                          | 5   | 18.5    | 10  |
|                          | 4   | 30      | 8   |
| *Bastion*                | 1   | 3.75    | 1   |
| **Docker Services**      |
| *Basic*                  | 1   | 7.5     | 2   |
| **CDH**                  |
| *Basic*                  | 7   | 105     | 28  |
| *Bastion*                | 1   | 2       | 1   |
| **Misc**                 |
| *Nat*                    | 1   | 2       | 1   |
| *Bosh*                   | 1   | 3.75    | 1   |
| **Subtotals**            | 21  | 172.5   | 52  |
| **OpenStack Components** |
| *Controller nodes*       | 3   | 72      | 36  |
| *Storage nodes*          | 3   | 72      | 12  |
| *Fuel nodes*             | 1   | 4       | 2   |
| **Total**                | 28  | 320.5   | 102 |

<br>
##### Without HA, 100 apps
| Options                  | QTY | RAM (GB) | CPU |
|--------------------------|-----|---------|-----|
| **Cloud Foundry**        |
| *CF components*          | 10  | 37.5    | 20  |
|                          | 1   | 7.5     | 2   |
| *Runners*                | 10  | 150     | 40  |
| *Bastion*                | 1   | 3.75    | 1   |
| **Docker Services**      |
| *Basic*                  | 1   | 30      | 8   |
| **CDH**                  |
| *Basic*                  | 10  | 150     | 40  |
| *Bastion*                | 1   | 3.75    | 1   |
| *+ Kerberos*             | 1   | 7.5     | 2   |
| **Misc**                 |
| *Nat*                    | 1   | 2       | 1   |
| *Bosh*                   | 1   | 3.75    | 1   |
| **Subtotals**            | 36  | 392     | 115 |
| **OpenStack Components** |
| *Controller nodes*       | 3   | 72      | 36  |
| *Storage nodes*          | 3   | 72      | 12  |
| *Fuel nodes*             | 1   | 4       | 2   |
| **Total**                | 43  | 543.75  | 166 |

<br>
##### HA, 100 apps

| Options                  | QTY | RAM (GB) | CPU |
|--------------------------|-----|---------|-----|
| **Cloud Foundry**        |
| *CF components*          | 20  | 75      | 40  |
|                          | 1   | 7.5     | 2   |
| *Runners*                | 16  | 240     | 64  |
| *Bastion*                | 1   | 3.75    | 1   |
| **Docker Services**      |
| *Basic*                  | 1   | 30      | 8   |
| **CDH**                  |
| *Basic*                  | 12  | 180     | 48  |
| *Bastion*                | 1   | 3.75    | 1   |
| *+ Kerberos*             | 1   | 7.5     | 2   |
| *+ HA*                   | 1   | 15      | 4   |
| **Misc**                 |
| *Nat*                    | 1   | 2       | 1   |
| *Bosh*                   | 1   | 3.75    | 1   |
| **Subtotals**            | 55  | 564.5   | 171 |
| **OpenStack Components** |
| *Controller nodes*       | 3   | 72      | 36  |
| *Storage nodes*          | 3   | 72      | 12  |
| *Fuel nodes*             | 1   | 4       | 2   |
| **Total**                | 62  | 715.25  | 223 |

<br>
##### HA, 1000 apps

| Options                  | QTY | RAM (GB) | CPU |
|--------------------------|-----|---------|-----|
| **Cloud Foundry**        |
| *CF components*          | 20  | 75      | 40  |
|                          | 1   | 7.5     | 2   |
| *Runners*                | 70  | 2100    | 560 |
| *Bastion*                | 1   | 3.75    | 1   |
| **Docker Services**      |
| *Basic*                  | 1   | 30      | 8   |
| **CDH**                  |
| *Basic*                  | 12  | 360     | 96  |
| *Bastion*                | 1   | 3.75    | 1   |
| *+ Kerberos*             | 1   | 7.5     | 2   |
| *+ HA*                   | 1   | 30      | 8   |
| **Misc**                 |
| *Nat*                    | 1   | 2       | 1   |
| *Bosh*                   | 1   | 3.75    | 1   |
| **Subtotals**            | 109 | 2619.5  | 719 |
| **OpenStack Components** |
| *Controller nodes*       | 3   | 72      | 36  |
| *Storage nodes*          | 3   | 72      | 12  |
| *Fuel nodes*             | 1   | 4       | 2   |
| **Total**                | 116 | 2761.25 | 770 |

The OpenStack instance data was retrieved from [here](https://docs.mirantis.com/openstack/fuel/fuel-6.0/planning-guide.html#sample-hardware-configuration-for-target-nodes).

This sizing chart does not include the necessary HDDs that should be directly attached to the CDH nodes.
