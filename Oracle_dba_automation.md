# Oracle DBA automation


### [ 1.] Create Trigger to auto start pdbs after database is started
Oracle pluggable database not being started manually after the services are started automatically.When a pluggable database is utilized and configured for Oracle 12c, it must be started manually as only the container database is started automatically. This is a known defect for Oracle 12c database and has been fixed in later releases of Oracle 12c.  If the pluggable database is not started manually, the error is displayed

The solution is to start the pluggable database manually in SQL*Plus or SQL Developer.
Steps:
Connect to SQL*Plus or SQL Developer as sysdba
Go to Start --> CMD
sqlplus /@//<hostname>:<port>/<service_name> as sysdba
Run query: alter pluggable database <pluggable_database_name> open;
Database altered

To resolve this issue permanently, run the trigger to open the pluggable databases when the Oracle instance is started.
Please execute the following in SQL*Plus or SQL Developer as sysdba:
```
CREATE OR REPLACE TRIGGER open_my_pdbs
AFTER STARTUP ON DATABASE
BEGIN
EXECUTE IMMEDIATE 'ALTER PLUGGABLE DATABASE ALL OPEN';
END open_my_pdbs;
/
```
