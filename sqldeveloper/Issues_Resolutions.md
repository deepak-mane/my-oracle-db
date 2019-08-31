# SQL Developer Issues Resolutions:

### [1.] Sqldeveloper Connection import failed with error oracle.jdevimpl.db.adapter.DatabaseProviderFactory1212
<b>Solution</b>
1. Navigate to C:\Users\%USERNAME%\AppData\Roaming\SQL Developer\system3.2.20.10.20\o.jdeveloper.db.connection.11.1.1.4.37.59.48
2. In connections.xml 
   - Find: oracle.jdevimpl.db.adapter.DatabaseProviderFactory1212
   - Replace: oracle.jdeveloper.db.adapter.DatabaseProvider
3. In config.xml which you are importing do the above and try importing

### [2.] Add JAva path in SQL Developer
<b>Solution</b>
When asked to provide Java path add : C:\Oracle\product\12.1.0\client_1\jdk\jre\bin\java.exe [One time activity]
