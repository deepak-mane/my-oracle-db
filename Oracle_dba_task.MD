# Oracle DBA Tasks :-

## Enable - Disable Archive Logging
```
When you run the database in NOARCHIVELOG mode, you disable the archiving of the redo log. If you want to take the backup of the database using RMAN then your database must be in ARCHIVELOG mode.
A database backup, together with online and archived redo log files, guarantees that you can recover all committed transactions in the event of OS or disk faluer.

Below are the steps required to enable archive log mode on an Oracle 10g/11g or 12c database.
Verify the database log mode.

[oracle@orahow ~]$ sqlplus / as sysdba

SQL*Plus: Release 11.2.0.4.0 Production on Sat Feb 03 04:05:02 2015

Copyright (c) 1982, 2013, Oracle.  All rights reserved.

Connected to:
Oracle Database 11g Enterprise Edition Release 11.2.0.4.0 – 64bit Production
With the Partitioning, Real Application Clusters, Automatic Storage Management, OLAP,
Data Mining and Real Application Testing options

SQL> archive log list
Database log mode              No Archive Mode
Automatic archival             Disabled
Archive destination            USE_DB_RECOVERY_FILE_DEST
Oldest online log sequence     25
Current log sequence           27

From the above output you can see that your database is in No Archive Mode. Note that Archive destination is USE_DB_RECOVERY_FILE_DEST. You can determine the path by looking at the parameter RECOVERY_FILE_DEST.

SQL> show parameter recovery_file_dest


 
NAME                                 TYPE        VALUE
———————————— ———– ——————————
db_recovery_file_dest                string      /u01/app/oracle/flash_recovery
                                                 _area
db_recovery_file_dest_size           big integer 2728M

By default, archive logs will be written to the flash recovery area also called FRA. If you don’t want to write archive logs to the FRA then you can set the parameter LOG_ARCHIVE_DEST_n to the new location where you wish to write the archive logs.

To set new new archive log destination, you can use the following command.

SQL> alter system set log_archive_dest_1=’LOCATION=/u01/app/oracle/oradata/orahow/arch’ scope = both;

System altered.

SQL> archive log list;
Database log mode              No Archive Mode
Automatic archival             Disabled
Archive destination            /u01/app/oracle/oradata/orahow/arch
Oldest online log sequence     26
Current log sequence           28
SQL>

Now we shutdown the database and bring it backup in mount mode.

SQL> shutdown immediate
Database closed.
Database dismounted.
ORACLE instance shut down.

SQL> startup mount
ORACLE instance started.

Total System Global Area  606806016 bytes
Fixed Size                  1376268 bytes
Variable Size             394268660 bytes
Database Buffers          205520896 bytes
Redo Buffers                5640192 bytes
Database mounted.
SQL>

Now set the database in archive log mode

SQL> alter database archivelog;

Database altered.

Finally open the database.
SQL> alter database open;

Database altered.


 
SQL> archive log list
Database log mode              Archive Mode
Automatic archival             Enabled
Archive destination            /u01/app/oracle/oradata/orahow/arch
Oldest online log sequence     26
Next log sequence to archive   28
Current log sequence           28

From the above output you can see that database is in archive log mode and automatic archival is also enabled.

For experiment or confirmation you can switch the log file to see that an archive is written to archive log location.

SQL> alter system switch logfile;

System altered.

SQL> host
[oracle@orahow ~]$ ls /u01/app/oracle/oradata/orahow/arch
1_28_812359664.dbf
[oracle@orahow ~]$ exit
exit

Disabling Archive Log Mode
The following are the steps required to disable archive log mode on an Oracle 10g/11g or 12c database.

Verify the database log mode.

[oracle@orahow ~]$ sqlplus / as sysdba

SQL*Plus: Release 11.2.0.4.0 Production on Sat Feb 03 04:05:02 2015

Copyright (c) 1982, 2013, Oracle.  All rights reserved.

Connected to:
Oracle Database 11g Enterprise Edition Release 11.2.0.4.0 – 64bit Production
With the Partitioning, Real Application Clusters, Automatic Storage Management, OLAP,
Data Mining and Real Application Testing options

SQL> archive log list
Database log mode              Archive Mode
Automatic archival             Enabled
Archive destination            /u01/app/oracle/oradata/orahow/arch
Oldest online log sequence     26
Next log sequence to archive   28
Current log sequence           28

SQL> shutdown immediate
Database closed.
Database dismounted.
ORACLE instance shut down.

SQL> startup mount
ORACLE instance started.

Total System Global Area  606806016 bytes
Fixed Size                  1376268 bytes
Variable Size             394268660 bytes
Database Buffers          205520896 bytes
Redo Buffers                5640192 bytes
Database mounted.
SQL>

To disable archive log mode use the below command.


 
SQL> alter database noarchivelog;

Database altered.

SQL> alter database open;

Database altered.

SQL> archive log list;
Database log mode              No Archive Mode
Automatic archival             Disabled
Archive destination            /u01/app/oracle/oradata/orahow/arch
Oldest online log sequence     26
Current log sequence           28
SQL>
As you can see, ARCHIVELOG mode has been disabled.

```
