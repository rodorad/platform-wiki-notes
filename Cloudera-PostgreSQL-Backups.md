
## Backing up PostgreSQL

* The PostgreSQL instance used by Cloudera is located on the _cdh-manager_ host. 

* The database is installed by the _cloudera-manager-server-db-2_ package, and is an postgresql 8.0 listening on port 7432.

* The default root user is _cloudera-scm_ and the default root password is located in the _/var/lib/cloudera-scm-server-db/data/generated_password.txt_ file:


* The backup script is scheduled to run automatically and store log information in syslog, located in the _/var/log/messages_ file. It is also possible to run the backup script manually as root user:
```
sudo su -
~/DumpPSQL.py
```

## Restoring PostgreSQL

To restore PostgreSQL databases or tables, you will need access to the AWS S3 <b>backups</b> bucket.

1. Download tar.xz file (ENV_PSQL_YY-MM-DDUhh_mm_ss.tar.xz) containing PostgreSQL all dump (PSQL.all.out) from AWS S3, with the following command:<br\>
   `aws s3 cp s3://backups/[file] . `<br\>

   To list all files in bucket use:<br\>
   `aws s3 ls s3://backups`

2. Unpack tar:<br\>
   `tar -xvJf [file].tar.xz`

   To restore all databases:<br\>
   `~/RestorePSQL.py [PSQL.all.out] all`

   To restore single database:<br\>
   `~/RestorePSQL.py -LIST=databasename [PSQL.all.out] dbs`

   To restore specified databases:<br\>
   `~/RestorePSQL.py -LIST=databasename1,...,databasenameN [PSQL.all.out] dbs`

   To reload roles privileges (to create/recreate users you have to use 'all' command):<br\>
   `~/RestorePSQL.py [PSQL.all.out] users`
