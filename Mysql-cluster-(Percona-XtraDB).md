# Mysql cluster (Percona XtraDB)
Created solution is based on:
* odd number of db nodes with synchronous replication (Percona XtraDB Galera Cluster 5.6)
* one backup server with standard mysql instance which fetches updates from one of the cluster db nodes through async replication and performs backups every day at night (one backup for one database)
* one haproxy instance which loadbalances requests to cb cluster nodes

Code for this module is stored in trustedanalytics/terraform-aws-intel repository.

Installation instructions along with coverage of needed parameters for:
* AWS are in: https://github.com/trustedanalytics/platform-wiki/wiki/Platform-Deployment-Procedure-in-AWS (will be done after PR merges)
* OpenStack will be added in the future.

Limitations of Percona XtraDB Galera Cluster 5.6 are described here: https://www.percona.com/doc/percona-xtradb-cluster/5.6/limitation.html , basically only innodb engine is supported.

Various other useful links:
* Possible alternatives: https://www.percona.com/live/mysql-conference-2013/sessions/evaluating-mysql-high-availability-alternatives
* Synchronous Multi Master replication: https://www.percona.com/doc/percona-xtradb-cluster/5.6/features/multimaster-replication.html 
* Mysql 5.6 multi threaded replication: https://www.percona.com/live/mysql-conference-2015/sites/default/files/slides/MySQL_MultiThreaded_Replication.pdf
* Standalone additional node with async replication (used for backups in this solution): http://www.severalnines.com/blog/how-set-asynchronous-replication-galera-cluster-standalone-mysql-server-gtid
