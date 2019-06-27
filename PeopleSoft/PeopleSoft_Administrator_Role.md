# PS Roles & Responsibilties:

1.       Configured dev, test, pre prod and production instances for HSCM,FSCM,ELM,HUB
2.       Monitoring daily internal server status.
3.       Configured PeopleSoft work stations for development team.
4.       Setting up report nodes and checking their posting status.
5.       Creating users in db and assigning required roles. [If has DBA privilidges]
```
SQL>SELECT * FROM DBA_ROLE_PRIVS;
SQL>SELECT * FROM DBA_SYS_PRIVS;
Sql>SELECT GRANTEE,PRIVILEGE FROM dba_sys_privs where grantee in(‘NABADM’,’POPLE’,’PS’);
Sql>SELECT GRANTEE,GRANTED_ROLE,DEFAULT_ROLE FROM dba_role_privs where grantee in(‘NABADM’,’POPLE’,’PS’);
SQL>CREATE USER RAJU IDENTIFIED BY RAJU DEFAULT TABLESPACE PSDEFAULT,TEMPORARY TABLESPACE PSTEMP;
SQL>GRANT CONNECT,PSREADONLY TO RAJU;
```
6.       Migrating projects from one environment to another environment using App designer/ STAT/ Phire.
7.       Taking compare reports and migrating projects to higher instances.
8.       Taking table backup using DM & at DB Level.
```
SET OUTPUT C:\N_GRADE_SETUP.DAT;
SET LOG C:\N_GRADE_SETUP.LOG;
```
9.       Applying Rule Packages & Non Rule Packages After Applying Taxupdate.
```
Main Menu->Setup HRMS->Product Related->Global Payroll & Absense Mgmt-> Elements                ->Manage Global Payroll Packages->Apply Rule Package
Main Menu->Setup HRMS->Product Related->Global Payroll & Absense Mgmt->Elements             ->Manage Global Payroll Packages->Export Non Rule Packages
```
10.   Tracking people code and sql and sending it to developers.
11.   Responsibilities for daily maintenance of App server, Web server, Process Scheduler server and DB server.
12.   Running the process of “Calculate Absence & Payroll” for salaries of Employees.
```
Main Menu->Global Payroll & Absence Mgmt->Calculate Absence & Payroll.
```
13.   Checking the processing statistics of Payroll Process.
```
Main Menu->Global Payroll & Absence Mgmt->Absence & Payroll Processing->Review Absence/Payroll Info/Process Statistics àUsing calendar id we can find Processing statistics.
```
14.   Enable Load Balancing on App server and Process scheduler servers(psappsrv.ubx).
15.   Creating users at application level and assigning them proper permissions lists and roles.
16.   Involved in troubleshooting the App server, Process scheduler and web server.
17.   Configuring IB, defining gateway, creating and configuring nodes for HSCM & FSCM.
18.   Responsible for bouncing App server, Web Server, Process scheduler as per request.
19.   Have been involved in refreshing dev, test from production DB using hot and cold backups
20.   Monitored the server & client queue status at several tuxedo domains.
21.   Deleting XML  files at OS Level using this command
```
find .  -name  '*NABPRODfiles*'  |  xargs rm -rf
```
22.   Checked & cleared the app server cache on regular basis as maintenance.
23.   Deleting  oraarch files in DB server  at OS level using this query
```
find  /oraarch* -mtime  +5  -exec rm {} \;
```
24.   Experience in PeopleSoft performance and tuning.
25.   Checking oraarch folder space size at DB level using this query
```
SQL> select trunc(COMPLETION_TIME,'DD') Day, thread#, round(sum(BLOCKS*BLOCK_SIZE)/1073741824) GB,count(*) Archives_Generated from v$archived_log group by trunc
(COMPLETION_TIME,'DD'),thread# order by 1;
```
26.   Apply fixes, updates & patches from demo, dev, test, UAT & Production Instances.
27.   Implemented forgot password functionality in Prod Server.
28.   Taking backups using RMAN.
```
#rman target /
RMAN>allocate channel CI format ‘/oradata2/exp_bkp’
RMAN>backup database
```
29.   Migrating sqr’s from env to another environment.
```
In windows using : ftp>>
In linux using : sftp>>
FileZilla software
```
30.   Good knowledge on taking Logical & Physical backups using export and Import.
31.   Configured SMTP settings in Appserver & Process scheduler configuration files.
```
C:\telnet 192.168.1.143  25     ------------------------------(proxy server,port)
```
32.   Monitor scheduled backups(NET VOLT)  & scheduled processes in production instances
33.   Handle security issues.
34.   Backup of Applications folder like PT8.56
35.   Creating & managing user profiles.
36.   Running Sysaudit & DDDaudit reports when required & when applying bundles.
37.   Monitoring table spaces size using this query
```
select TABLESPACE_NAME,USED_SPACE,TABLESPACE_SIZE,USED_PERCENT from DBA_TABLESPACE_USAGE_METRICS order by 4 desc;
```
38.   Checking current users at DB Level & Application level(using Console)
39.   Checking DB in sync or not b/w DC& DR Servers using this query.
```
SQL>SELECT MAX(SEQUENCE#) FROM V$ARCHIVED_LOG;
SQL>SELECT MAX(SEQUENCE#) FROM V$ARCHIVED_LOG WHERE APPLIED='YES';
```
40.   Monitor status of Appserver, Web server, Process Scheduler in DEV, TEST & Production instances.
41.   Updating statistics at DB Level using this query
```
SQL> exec dbms_stats.gather_schema_stats( '&schema', dbms_stats.auto_sample_size);
sql>exec dbms_stats.gather_schema_stats('NABADM')------------------------------It is for schema level
sql>exec sys.dbms_utility.analyze_schema ('NABADM', 'ESTIMATE')---------------This is old one for schema level
SQL> exec dbms_stats.gather_schema_stats( '&schema', dbms_stats.auto_sample_size);-----This is for schema level(New one)
Enter value for schema: NABADM
sql> exec dbms_stats.gather_system_stats --------------------------------------It is for database level
sql> exec dbms_stats.gather_table_stats('NABADM','TABLENAME')------------------It is for table level
```
42.   Refreshing the env in week end.
43.   Reindexing tables at DB Level using this query
```
SQL> SELECT INDEX_NAME,BLEVEL FROM DBA_INDEXES WHERE BLEVEL>3;
SQL>ALTER INDEX PS_GP_RSLT_ACUM REBUILD ONLINE;
```
44.   Checking free disk space on drives
45.   Creating tablespaces using this query
```
create tablespace AMAPP datafile 'E:\oracle\oradata\NDEV\amapp.dbf' size 10485760 autoextend on maxsize 34359721984 default storage ( initial 65536 minextents 1 maxextents unlimited) ;
```
46.   Monitoring  all server CPU performance if slowness occur at application level.
47.   Shifting tablespaces if DB size drive is full.
```
Sql> Alter tablespace PSIMAGE offline;
Sql> ALTER DATABASE RENAME FILE 'e:\oracle\oradata\NDEV\PSIMAGE.DBF' TO      'e:\oracle\oradata\NDEV\PSIMAGE.DBF';
Sql> Alter tablespace PSIMAGE online;
```
48.   Accepting tickets & assigning to respective resource
49.   Checking  appserver status , process scheduler status and deleting cache .
50.   Clear spaces in servers
51.   Migrating projects to higher instances
52.   Monitoring the run status of a processes.
53.   Monitoring and maintaining webmail(issue tracker)
54.   Checking the daily backup
55.   Check portal logins & create users in PIA
56.   Supporting & solving the issues on call to the client.
57.   Did pre & post activities of DB Refresh.
```
(a)    Run security import.dms & security export.dms scripts using DM
(b)   Updating  DB Name in PSDBOWNER, PSACCESSPRFL & PSOPRDEFN Tables.
(c)    Need to run appmsgpurgeall.dms, rptclr.dms, prcsclr.dms scripts
(d)   Updating email ids like PSUSEREMAIL, PS_ROLEXLATOPR, PSOPRDEFN, PS_EMAIL_ADDRESSES.( set emailed=’ ‘;)
(e)   update psoptions set GUID=' ';
```
58.   Increasing/Decreasing the size of the mounts or Directories in Unix: [If has Linux Admin Privilidges]
```
(a)   chfs –a  size=+5G /psoft( It's for increasing the size of psoft folder)
(b)   chfs –a  size=-1G /psoft ( It's for decreasing the size of psoft folder)
```
59.   Taking AWR,ADDM,ADR Reports in oracle db to know the status or performance of DB. 
```
Awr: It contain the whole performance of the db(Automatic workload Repository.
Addm: It will verify or gives about AWR Report performance.(Automatic DB Diagnostic monitor)
Adr:  ADR is the diagnostic repository which is meant for storage of the error related information , useful for debugging and troubleshooting.(Automatic Diagnostic Repository)

oracle@nabdcdb/#cd $ORACLE_HOME
oracle@nabdcdb/oracle/orahome#cd rdbms/admin/ls awr*
Go to sql prompt with sysdba and run the script as follows:
Sql>@/oracle/orahome/rdbms/admin/awrrpt.sql
Sql>@/oracle/orahome/rdbms/admin/addmrrpt.sql
Sql>@/oracle/orahome/rdbms/admin/ashrpt.sql
After running this scripts it will ask :
Report Name:rajutodaydate
Start date:12118
End date:13191       Ex: select for one day.O/P will store in the below path
D:\app\oracle\product\11.2.0\dbhome_1\RDBMS\Admin\       ------In windows
/oracle/orahome/rdbms/admin/  ----------------In linux
```
60.   Checking the performance of long running queries using explain
```
Sql>explain plan for (copy the long running query and press enter)
Sql>@/oracle/orahome/rdbms/admin/utlxpls.sql;  (It will display the performance of the query)
SQL> @E:\ORACLE\product\11.2.0\dbhome_1\RDBMS\ADMIN\utlxpls.sql(this in windows)
```
