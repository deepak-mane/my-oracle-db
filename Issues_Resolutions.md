# Issues Resolutions


### [1.] Why would Oracle.ManagedDataAccess not work when Oracle.DataAccess does?
<b>Problem :</b>
I'm developing a very simple application which I intend to use to troubleshoot an issue I am having on a few machines but before I even got that far I ran into a few issues, including cpu architecture differences and Oracle database libraries.

I have a database server listed in tnsnames.ora, sitting in my C:\oracle\11g\network\admin directory. If I tnsping this server I get the desired response. If I code my C# program to connect to this server with the following code using Oracle.DataAccess.Client, it works.
```
string connectionString = "Data Source=DSDSDS;User Id=UNUNUN;Password=PWPWPW;";
DataTable dataTable = new DataTable();

using (var connection = new OracleConnection(connectionString)) {
    connection.Open();
    using (var command = new OracleCommand()) {
        command.Connection = connection;
        command.CommandText = sql;
        command.CommandType = CommandType.Text;
        using (var oda = new OracleDataAdapter(command)) {
            oda.Fill(dataTable);
        }
    }
}
```
However Oracle.DataAccess is dependent on the architecture of the system it runs on. I saw that there is another library Oracle.ManagedDataAccess which is architecture independent. When I use this library it no longer is able to connect to the server. An ORA-12545: Network Transport: Unable to resolve connect hostname is thrown.

Why is this the case? What is different between these two libraries because based upon what I've read thus far this shouldn't be an issue.

Extra information:

%ORACLE_HOME% and %TNS_ADMIN% are NOT defined (remember that tnsping and Oracle.DataAccess work)
PATH has C:\oracle\11g\BIN defined.
My machine only has one tnsnames.ora file
If I move tnsnames.ora to the same location as my .exe file, it works. Why can Oracle.DataAccess find tnsnames.ora in the C:\oracle\11g\network\admin directory but Oracle.ManagedAccess cannot?

<b>Solution :</b>
The order of precedence for resolving TNS names in ODP.NET, Managed Driver is this (see here):
1. data source alias in the 'dataSources' section under section in the .NET config file.
1. data source alias in the tnsnames.ora file at the location specified by 'TNS_ADMIN' in the .NET config file.
1. data source alias in the tnsnames.ora file present in the same directory as the .exe.
1. data source alias in the tnsnames.ora file present at %TNS_ADMIN% (where %TNS_ADMIN% is an environment variable setting).
1. data source alias in the tnsnames.ora file present at %ORACLE_HOME%\network\admin (where %ORACLE_HOME% is an environment variable setting).

I believe the reason your sample works with Oracle.DataAccess but not with Oracle.ManagedDataAccess is that Windows registry based configuration is not supported for the latter (see documentation) - the ODP.NET installation sets an ORACLE_HOME registry key (HLKM\SOFTWARE\Oracle\Key_NAME\ORACLE_HOME) which is recognized only by the unmanaged part.
