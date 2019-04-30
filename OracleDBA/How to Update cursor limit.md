# How to update cursor limit for ORA-01000: maximum open cursors exceed

- SQL: Find cursor limit
 Role needed : Normal User
```
select max(a.value) as highest_open_cur, p.value as max_open_cur
  from v$sesstat a, v$statname b, v$parameter p
  where a.statistic# = b.statistic# 
  and b.name = 'opened cursors current'
  and p.name= 'open_cursors'
  group by p.value;
```
- SQL : To Update Cursor limit
 Role needed : sysdba

 Assuming that you are using a spfile to start the database
 
 ```
 alter system set open_cursors = 1000 scope=both;
 ```
 
If you are using a pfile instead, you can change the setting for the running instance

```
alter system set open_cursors = 1000 
```

You would also then need to edit the parameter file to specify the new open_cursors setting. It would generally be a good idea to restart the database shortly thereafter to make sure that the parameter file change works as expected (it's highly annoying to discover months later the next time that you reboot the database that some parameter file change than no one remembers wasn't done correctly).

I'm also hoping that you are certain that you actually need more than 300 open cursors per session. A large fraction of the time, people that are adjusting this setting actually have a cursor leak and they are simply trying to paper over the bug rather than addressing the root cause.
