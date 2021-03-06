
## Backing up MySQL

* The MySQL instance used by Cloudera is located on the _cdh-manager_ host.
* The MySQL login and password are stored in the Ansible* vault.

* The backup script is scheduled to run automatically; log information is stored in syslog, located in the _/var/log/messages_ file. <br\>
* It is also possible to perform run the backup script manually by executing the script as root user:

    ```
    sudo su -
    cd /opt/mysql_backup/
    ./mysql_backup.sh
    ```



## Restoring MySQL

To restore MySQL databases or tables, you will need access to the AWS S3 <i>backups</i> bucket.

1. Download the tar.gz file containing MySQL database dumps ([environment_name]_mysql_backup_YYYY-MM-DD-hh-mm.tar.xz) from AWS S3:<br\>
   `aws s3 cp s3://backups/[tar_file] . ` <br\>

   To list all files in the bucket: <br\>
   `aws s3 ls s3://backups `
  
2. Unpack the tar file:<br\>
   `tar -xvJf [tar_file].tar.xz`
   
   To restore all databases:<br\>
   `mysql -u[user_name] -p[password] <  mysql_all_[YYYY-MM-DD-hh-mm].sql`

  To restore a single database:<br\>
   `mysql -u[user_name] -p[password] <  mysql_[db_name]_[YYYY-MM-DD-hh-mm].sql`
   
  To restore a single table from a database dump:<br\>
   ```
(sed -n -e'/MySQL/,/Table structure for/p' mysql_[db_name]_[YYYY-MM-DD-hh-mm].sql; \
sed -n -e'/Table structure for.*`[table_name]/,/Table structure for/p' mysql_[db_name]_[YYYY-MM-DD-hh-mm].sql) \
   | mysql -u[user_name] -p[password]
```
