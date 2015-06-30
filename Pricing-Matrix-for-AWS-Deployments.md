
The following breakdowns are only suggestions.  Please note:
 - **The deployment phase requires approximately 10 more instances than the total claimed by the platform after the deployment is finished.**
 - Price estimates are based on one-year reserved instance prices for US-East-1 region.
 - Assumes Cloud Foundry apps are 1 GB in size, with low CPU and disk utilization.
 - The workload will ultimately determine the horizontal and vertical sizing.
 - Docker Services should **never** be run in Production mode. 
 - Sizing for CDH environments must be confirmed.

<br>
##### Single availability zone, 10 apps (smallest possible)

|Options|Size|QTY|$/Hr|Ext$/Hr|RAM (GB)|CPU|
|-----------|------------|----:|------:|---------:|---------:|----:|
|**Cloud Foundry**|
||c3.large|5|0.105|0.525|18.75|10|
||m3.large|4|0.14|0.56|30|8|
|**Docker Services**|
||m3.large|1|0.14|0.14|7.5|2|
|**CDH**|
|*Basic*|m3.xlarge|7|0.28|1.96|105|28
|*+ Kerberos*|||||||
|*+ HA(N\A)*|||||||
|**Misc**|
|*Nat*|t2.small|1|0.026|0.026|2|1|
|*Bastion*|m3.medium|1|0.07|0.07|3.75|1|
|*Bosh*|m3.medium|1|0.07|0.07|3.75|1|
|*CDH-Launcher*|t2.small|1|0.026|0.026|2|1|
||
|**Totals**||21||3.377|172.75|52||


<br>
##### Single availability zone, 100 apps

|Options|Size|QTY|$/Hr|Ext$/Hr|RAM (GB)|CPU|
|-----------|------------|----:|------:|---------:|---------:|----:|
|**Cloud Foundry**|
||c3.large|10|0.105|1.05|37.5|20|
||m3.large|1|0.14|0.14|7.5|2|
||m3.xlarge|10|0.28|2.8|150|40|
||m3.2xlarge|0|0.56|0|0|0|
|**Docker Services**|
|*Basic*|m3.2xlarge|1|0.56|0.56|30|8
|**CDH**|
|*Basic*|m3.xlarge|10|0.28|2.8|150|40
|*+ Kerberos*|m3.large|1|0.14|0.14|7.5|2|
|*+ HA(N\A)*|||||||
|**Misc**|
|*Nat*|t2.small|1|0.026|0.026|2|1|
|*Bastion*|m3.medium|1|0.07|0.07|3.75|1|
|*Bosh*|m3.medium|1|0.07|0.07|3.75|1|
||
|**Totals**||36||7.67|392|115||


<br>
##### Dual availability zone (HA), 100 apps

|Options|Size|QTY|$/Hr|Ext$/Hr|RAM (GB)|CPU|
|-----------|------------|----:|------:|---------:|---------:|----:|
|**Cloud Foundry**|
||c3.large|20|0.105|2.1|75|40|
||m3.large|1|0.14|0.14|7.5|2|
||m3.xlarge|16|0.28|4.48|240|64|
||m3.2xlarge|0|0.56|0|0|0|
|**Docker Services**|
|*Basic*|m3.2xlarge|1|0.56|0.56|30|8
|**CDH**|
|Basic|m3.xlarge|12|0.28|3.36|180|48
|*+ Kerberos*|m3.large|1|0.14|0.14|7.5|2|
|*+ HA*|m3.xlarge|1|0.28|0.28|15|4
|**Misc**|
|*Nat*|t2.small|1|0.026|0.026|2|1|
|*Bastion*|m3.medium|1|0.07|0.07|3.75|1|
|*Bosh*|m3.medium|1|0.07|0.07|3.75|1|
||
|**Totals**||55||11.22|564.5|171||


<br>
##### Dual availability zone (HA), 1000 Apps

|Options|Size|QTY|$/Hr|Ext$/Hr|RAM (GB)|CPU|
|-----------|------------|----:|------:|---------:|---------:|----:|
|**Cloud Foundry**|
||c3.large|20|0.105|2.1|75|40|
||m3.large|1|0.14|0.14|7.5|2|
||m3.xlarge|0|0.28|0|0|0|
||m3.2xlarge|70|0.56|39.2|2100|560|
|**Docker Services**|
|*Basic*|m3.2xlarge|1|0.56|0.56|30|8
|**CDH**|
|*Basic*|m3.2xlarge|12|0.56|6.72|360|96|
|*+ Kerberos*|m3.large|1|0.14|0.14|7.5|2|
|*+ HA*|m3.2xlarge|1|0.56|0.56|30|8|
|**Misc**|
|*Nat*|t2.small|1|0.026|0.026|2|1|
|*Bastion*|m3.medium|1|0.07|0.07|3.75|1|
|*Bosh*|m3.medium|1|0.07|0.07|3.75|1|
||
|**Totals**||109||49.59|2619.5|719||