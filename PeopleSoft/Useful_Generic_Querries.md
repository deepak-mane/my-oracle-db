# Useful Generic Querries:

## TroubleShooting

### You can use this sql statement to get the history for any date:
```
SELECT * FROM V$SQL V where  to_date(v.FIRST_LOAD_TIME,'YYYY-MM-DD hh24:mi:ss') > sysdate - 60
```
For recent SQL:
```
select * from v$sql
```
For history:
```
select * from dba_hist_sqltext
```

[REF](http://ajaypeoplesoft.blogspot.in/2014/06/some-nice-to-have-sql-scripts.html)

## Category: Components, Component-Interfaces related queries
01) SQL Query to find search records in a CI

```
SELECT SEARCHRECNAME, ADDSRCHRECNAME
FROM PSBCDEFN
WHERE BCNAME = 'AJ_MAP_XLAT_TBL_CI';
```

02) SQL QUERY TO FIND OUT THE RECORDS EXPOSED BY A CI

```
SELECT DISTINCT RECNAME
FROM PSBCITEM
WHERE BCNAME = 'AJ_MAP_XLAT_TBL_CI';
```

03) SQL Query to find out the Component associated with a Component Interface

```
SELECT BCPGNAME,MARKET,MENUNAME
FROM PSBCDEFN
WHERE BCNAME = 'AJ_MAP_XLAT_TBL_CI';
```

04) Identify which USER, ROLE and PERMISSION-LIST has access to a Component Interface

```
SELECT DISTINCT R.ROLEUSER AS USER_IDS,
  C.ROLENAME               AS ROLE,
  P.CLASSID                AS PERMISSION_LIST
FROM PSROLEUSER R,
  PSROLECLASS C,
  PSAUTHBUSCOMP P
WHERE R.ROLENAME = C.ROLENAME
AND P.CLASSID    = C.CLASSID
AND P.BCNAME     = 'AJ_MAP_XLAT_TBL_CI'
ORDER BY 1,2,3;
```

05) Find all records under a specified component.

```
SELECT DISTINCT RECNAME
FROM PSRECDEFN
WHERE ( RECNAME IN
  (SELECT RECNAME
  FROM PSPNLFIELD
  WHERE PNLNAME IN
    (SELECT DISTINCT B.PNLNAME
    FROM PSPNLGROUP A,
      PSPNLFIELD B
    WHERE (A.PNLNAME = B.PNLNAME
    OR A.PNLNAME     = B.SUBPNLNAME)
    AND A.PNLGRPNAME = 'AJ_MAP_XLAT_TBL'
    )
  )
OR RECNAME IN
  (SELECT RECNAME
  FROM PSPNLFIELD
  WHERE PNLNAME IN
    (SELECT DISTINCT B.SUBPNLNAME
    FROM PSPNLGROUP A,
      PSPNLFIELD B
    WHERE (A.PNLNAME = B.PNLNAME
    OR A.PNLNAME     = B.SUBPNLNAME)
    AND A.PNLGRPNAME = 'AJ_MAP_XLAT_TBL'
    )
  ) )
AND RECNAME <> ' '
ORDER BY RECNAME ASC;
```

06) SQL query to get all the child records for a parent record:

```
SELECT RECNAME FROM PSRECDEFN
CONNECT BY PRIOR RECNAME=PARENTRECNAME
START WITH PARENTRECNAME = 'VOUCHER';
```

## Category: Portal Navigation
01) Find the navigation to a Component

```
SELECT P1.PORTAL_NAME, P6.PORTAL_LABEL, P5.PORTAL_LABEL, P4.PORTAL_LABEL, P3.PORTAL_LABEL,
P2.PORTAL_LABEL, P1.PORTAL_LABEL
FROM PSPRSMDEFN P1
LEFT JOIN PSPRSMDEFN P2 ON P2.PORTAL_NAME = P1.PORTAL_NAME AND P2.PORTAL_OBJNAME = P1.PORTAL_PRNTOBJNAME
LEFT JOIN PSPRSMDEFN P3 ON P3.PORTAL_NAME = P2.PORTAL_NAME AND P3.PORTAL_OBJNAME = P2.PORTAL_PRNTOBJNAME
LEFT JOIN PSPRSMDEFN P4 ON P4.PORTAL_NAME = P3.PORTAL_NAME AND P4.PORTAL_OBJNAME = P3.PORTAL_PRNTOBJNAME
LEFT JOIN PSPRSMDEFN P5 ON P5.PORTAL_NAME = P4.PORTAL_NAME AND P5.PORTAL_OBJNAME = P4.PORTAL_PRNTOBJNAME
LEFT JOIN PSPRSMDEFN P6 ON P6.PORTAL_NAME = P5.PORTAL_NAME AND P6.PORTAL_OBJNAME = P5.PORTAL_PRNTOBJNAME
WHERE P1.PORTAL_REFTYPE = 'C' AND P1.PORTAL_URI_SEG2 = 'ACCOUNTINGENTRY';
```

02) To the navigation of a particular process name, say 'FS_JGEN'

```
SELECT LPAD(' ', 10*LEVEL-10) || PS.PORTAL_LABEL
FROM PSPRSMDEFN PS
START WITH PS.PORTAL_URI_SEG2 IN (SELECT PSB.PNLGRPNAME FROM PS_PRCSDEFNPNL PSB WHERE PSB.PRCSNAME = 'FS_JGEN')
AND PS.PORTAL_URI_SEG3 IN ('GBL','USA')
CONNECT BY PRIOR PS.PORTAL_PRNTOBJNAME = PS.PORTAL_OBJNAME
AND PS.PORTAL_NAME = 'EMPLOYEE' AND PS.PORTAL_LABEL <> 'Root';
```

## Category: Process Scheduler
01) Process Scheduler Server(s) status

```
SELECT S.SERVERNAME, X.XLATSHORTNAME,X.FIELDVALUE,S.MAXCPU,S.PRCSDISKSPACE,S.LASTUPDDTTM
FROM PSSERVERSTAT S, PSXLATITEM X
WHERE X.FIELDNAME = 'SERVERSTATUS' AND X.FIELDVALUE = S.SERVERSTATUS;
```

02) Processes running since yesterday

```
SELECT A.PRCSINSTANCE,
  A.OPRID,
  B.XLATLONGNAME "RUN_STATUS",
  A.JOBINSTANCE,
  A.PRCSJOBNAME,
  A.PRCSNAME,
  A.PRCSTYPE,
  A.BEGINDTTM,
  A.ENDDTTM,
  TO_CHAR((A.ENDDTTM - A.BEGINDTTM),'HH.MM.SS')
FROM PSPRCSRQST A,
  PSXLATITEM B
WHERE B.FIELDNAME = 'RUNSTATUS'
AND B.FIELDVALUE  = A.RUNSTATUS
AND A.BEGINDTTM   > (SYSDATE - 1)
ORDER BY A.PRCSINSTANCE DESC;
```

## Category: Users, Roles and Permissions
01) Determine the list of query security trees that an oprid has access to

```
SELECT distinct P.TREE_NAME,P.ACCESS_GROUP,P.ACCESSIBLE
FROM PS_SCRTY_ACC_GRP P, PSROLECLASS C, PSROLEUSER R
WHERE R.ROLENAME = C.ROLENAME
AND P.CLASSID = C.CLASSID
AND R.ROLEUSER = 'AGGARWAA'
AND P.ACCESSIBLE = 'Y'
ORDER BY 1,2;
```

02) Identify which USERs, ROLEs and PERMISSION-LISTs have access to load data using ExceltoCI utility

```
SELECT DISTINCT R.ROLEUSER AS USER_IDS,
  C.ROLENAME               AS ROLE,
  P.CLASSID                AS PERMISSION_LIST
FROM PSROLEUSER R,
  PSROLECLASS C,
  PSAUTHWEBLIBVW P
WHERE R.ROLENAME = C.ROLENAME
AND P.CLASSID    = C.CLASSID
AND P.MENUNAME   = 'WEBLIB_SOAPTOCI'
ORDER BY 1,2,3;
```

03) Identify through which user, role and permission-list access to a particular component interface

```
SELECT DISTINCT R.ROLEUSER AS USER_IDS, C.ROLENAME as ROLE, P.CLASSID AS PERMISSION_LIST
FROM PSROLEUSER R, PSROLECLASS C, PSAUTHBUSCOMP P
WHERE R.ROLENAME = C.ROLENAME
AND P.CLASSID = C.CLASSID
AND P.BCNAME = 'AJ_MAP_XLAT_TBL_CI'
ORDER BY 1,2,3;
```

04) Roles assigned to a permission-list

```
SELECT B.ROLENAME, B.CLASSID AS PERMISSION_LIST
FROM PSCLASSDEFN A, PSROLECLASS B
WHERE A.CLASSID = B.CLASSID AND A.CLASSID = 'PTPT1200'
ORDER BY 1,2;
```

05) List all user ids that are assigned to a specific permission-list

```
SELECT DISTINCT C.ROLEUSER AS USER_IDS
FROM PSCLASSDEFN A,
  PSROLECLASS B,
  PSROLEUSER C
WHERE A.CLASSID = B.CLASSID
AND B.ROLENAME  = C.ROLENAME
AND A.CLASSID   = 'PTPT1200';
06) Role AND USER ids assigned TO a permission list
SELECT C.ROLEUSER AS USER_IDS,
  B.ROLENAME      AS ROLE
FROM PSCLASSDEFN A,
  PSROLECLASS B,
  PSROLEUSER C
WHERE A.CLASSID = B.CLASSID
AND B.ROLENAME  = C.ROLENAME
AND A.CLASSID   = 'PTPT1200'
ORDER BY 1,2;
```

## Category: Users logged in
01) Number of users connected to the environment in last 30 days
```
SELECT TO_CHAR(A.LOGINDTTM,'YYYY-MM-DD'),count(distinct A.OPRID) "Users"
FROM PSACCESSLOG A
WHERE TO_CHAR(A.LOGINDTTM,'YYYY-MM-DD') >= TO_CHAR(SYSDATE - 30,'YYYY-MM-DD')
GROUP BY TO_CHAR(A.LOGINDTTM,'YYYY-MM-DD')
ORDER BY TO_CHAR(A.LOGINDTTM,'YYYY-MM-DD') DESC;
```
02) List of users connected to the environment in last 30 days, with connections count
```
SELECT TO_CHAR(A.LOGINDTTM,'YYYY-MM-DD'),
  A.OPRID,
  SUM(1) "Connections"
FROM PSACCESSLOG A
WHERE TO_CHAR(A.LOGINDTTM,'YYYY-MM-DD') >= TO_CHAR(SYSDATE - 30,'YYYY-MM-DD')
GROUP BY TO_CHAR(A.LOGINDTTM,'YYYY-MM-DD'),
  A.OPRID
ORDER BY TO_CHAR(A.LOGINDTTM,'YYYY-MM-DD') DESC,
  A.OPRID;
```
03) NUMBER OF connections TO the environment IN last 30 days
```
SELECT TO_CHAR(A.LOGINDTTM,'YYYY-MM-DD'),
  COUNT(A.OPRID) "Connections"
FROM PSACCESSLOG A
WHERE TO_CHAR(A.LOGINDTTM,'YYYY-MM-DD') >= TO_CHAR(SYSDATE - 30,'YYYY-MM-DD')
GROUP BY TO_CHAR(A.LOGINDTTM,'YYYY-MM-DD')
ORDER BY TO_CHAR(A.LOGINDTTM,'YYYY-MM-DD') DESC;
```
## Category: SQLs related to PS-Query
01) If you forget the name of a query, but know that it contains certain fields for sure,
the following SQL could be helpful
```
SELECT A.OPRID, A.QRYNAME, A.DESCR
FROM PSQRYDEFN A
WHERE A.QRYNAME IN (SELECT B1.QRYNAME FROM PSQRYFIELD B1 WHERE B1.FIELDNAME = 'OPRID')
AND A.QRYNAME IN (SELECT B1.QRYNAME FROM PSQRYFIELD B1 WHERE B1.FIELDNAME = 'RUN_CNTL_ID')
AND A.QRYNAME IN (SELECT B1.QRYNAME FROM PSQRYFIELD B1 WHERE B1.FIELDNAME = 'JOURNAL_ID')
AND A.QRYNAME IN (SELECT B1.QRYNAME FROM PSQRYFIELD B1 WHERE B1.FIELDNAME LIKE '%CURRENCY%')
AND A.QRYNAME IN (SELECT B1.QRYNAME FROM PSQRYFIELD B1 WHERE B1.FIELDNAME LIKE '%AMT%') ;
```
02) Changing the OPRID(Owner) of a private query
(Please change the new OPRID, OLDOPRID and Private Query name appropriately)
```
update psqrydefn set OPRID = 'NEWOPRID' where OPRID = 'OLDOPRID' AND qryname = 'PRIVATE_QUERY_NAME';
update psqrydefnlang set OPRID = 'NEWOPRID' where OPRID = 'OLDOPRID' AND qryname = 'PRIVATE_QUERY_NAME';
update psqrybind set OPRID = 'NEWOPRID' where OPRID = 'OLDOPRID' AND qryname = 'PRIVATE_QUERY_NAME';
update psqrybindlang set OPRID = 'NEWOPRID' where OPRID = 'OLDOPRID' AND qryname = 'PRIVATE_QUERY_NAME';
update psqryexeclog set OPRID = 'NEWOPRID' where OPRID = 'OLDOPRID' AND qryname = 'PRIVATE_QUERY_NAME';
update psqryexpr set OPRID = 'NEWOPRID' where OPRID = 'OLDOPRID' AND qryname = 'PRIVATE_QUERY_NAME';
update psqryselect set OPRID = 'NEWOPRID' where OPRID = 'OLDOPRID' AND qryname = 'PRIVATE_QUERY_NAME';
update psqrycriteria set OPRID = 'NEWOPRID' where OPRID = 'OLDOPRID' AND qryname = 'PRIVATE_QUERY_NAME';
update psqryfield set OPRID = 'NEWOPRID' where OPRID = 'OLDOPRID' AND qryname = 'PRIVATE_QUERY_NAME';
update psqryfieldlang set OPRID = 'NEWOPRID' where OPRID = 'OLDOPRID' AND qryname = 'PRIVATE_QUERY_NAME';
update psqryfielddep set OPRID = 'NEWOPRID' where OPRID = 'OLDOPRID' AND qryname = 'PRIVATE_QUERY_NAME';
update psqryrecord set OPRID = 'NEWOPRID' where OPRID = 'OLDOPRID' AND qryname = 'PRIVATE_QUERY_NAME';
update psqrystats set OPRID = 'NEWOPRID' where OPRID = 'OLDOPRID' AND qryname = 'PRIVATE_QUERY_NAME';
update psqrydel set OPRID = 'NEWOPRID' where OPRID = 'OLDOPRID' AND qryname = 'PRIVATE_QUERY_NAME';
update PSQRYXFORM set OPRID = 'NEWOPRID' where OPRID = 'OLDOPRID' AND qryname = 'PRIVATE_QUERY_NAME';
update PSQRYTRANS set OPRID = 'NEWOPRID' where OPRID = 'OLDOPRID' AND qryname = 'PRIVATE_QUERY_NAME';
update PSQRYFAVORITES set QRYOWNER = 'NEWOPRID' where QRYOWNER = 'OLDOPRID' AND qryname = 'PRIVATE_QUERY_NAME';
```
Note: 
a) For making a private query as public, change the OPRID from non-blank to single space.
b) You may use the navigation

```
PEOPLETOOLS»UTILITIES»ADMINISTRATION—»QUERY-ADMINISTRATION
and assign a new owner, delete query or rename query.
```

## Category: Trees
01) Getting the Tree Branches, Nodes and detail ranges for a tree.

```
SELECT J.TREE_NAME,
  J.TREE_NODE,
  N.DESCR "NODE NAME",
  J.TREE_BRANCH,
  K.RANGE_FROM,
  K.RANGE_TO
FROM PSTREENODE J,
  PS_TREE_NODE_TBL N,
  PSTREELEAF K
WHERE J.SETID   = 'SHARE' — Change the SETID here,          AS appropriate
AND J.TREE_NAME = 'ACCTROLLUP' — Change the Tree Name here, AS appropriate
AND J.EFFDT     =
  (SELECT MAX(ED.EFFDT)
  FROM PSTREENODE ED
  WHERE J.SETID       = ED.SETID
  AND J.SETCNTRLVALUE = ED.SETCNTRLVALUE
  AND J.TREE_NAME     = ED.TREE_NAME
  AND ED.EFFDT       <= SYSDATE
  )
AND EXISTS
  (SELECT 'x'
  FROM PSTREENODE
  WHERE J.SETID     = J.SETID
  AND SETCNTRLVALUE = J.SETCNTRLVALUE
  AND TREE_NAME     = J.TREE_NAME
  AND EFFDT        <= J.EFFDT
  AND TREE_NODE_NUM = J.PARENT_NODE_NUM
  )
AND N.SETID     = J.SETID
AND N.TREE_NODE = J.TREE_NODE
AND N.EFFDT     =
  (SELECT MAX(ND.EFFDT)
  FROM PS_TREE_NODE_TBL ND
  WHERE N.SETID   = ND.SETID
  AND N.TREE_NODE = ND.TREE_NODE
  AND ND.EFFDT   <= SYSDATE
  )
AND J.SETID     = K.SETID
AND J.TREE_NAME = K.TREE_NAME
AND K.EFFDT    >= J.EFFDT
AND K.EFFDT     =
  (SELECT MAX(KD.EFFDT)
  FROM PSTREELEAF KD
  WHERE K.SETID       = KD.SETID
  AND K.SETCNTRLVALUE = KD.SETCNTRLVALUE
  AND K.TREE_NAME     = KD.TREE_NAME
  AND KD.EFFDT       <= SYSDATE
  )
AND J.TREE_BRANCH   = K.TREE_BRANCH
AND J.TREE_NODE_NUM = K.TREE_NODE_NUM ;
```
Note: The AND EXISTS line is added to exclude orphan nodes.

A query that will take a PeopleSoft role name as an input and returns all pages that could be access by that role. The query will also indicate what kind of operations a user can perform on that page. Example, Add Update/Display and so forth.
http://www.compshack.com/peoplesoft/list-pages-peoplesoft-role-name-can-access

```
SELECT   b.menuname, b.barname, b.baritemname, d.pnlname, c.pageaccessdescr
    FROM psroleclass a,
         psauthitem b,
         pspgeaccessdesc c,
         pspnlgroup d,
         psmenuitem e
   WHERE a.classid = b.classid
     AND d.pnlgrpname = e.pnlgrpname
     AND b.menuname = e.menuname
     AND b.barname = e.barname
     AND b.baritemname = e.itemname
     AND a.rolename = :rolename
     AND b.authorizedactions = c.authorizedactions
GROUP BY b.menuname, b.barname, b.baritemname, d.pnlname, c.pageaccessdescr
ORDER BY d.pnlname;
```

02) Query to find which Roles have access to certain PAGE

```
SELECT rc.rolename,
  rd.descr ,
  a.classid,
  c.classdefndesc
FROM psauthitem a,
  psclassdefn c,
  psroleclass rc,
  psroledefn rd
WHERE a.pnlitemname = 'LM_IN_DFLT_GEN'
AND a.classid       = c.classid
AND a.classid       = rc.classid
AND rc.rolename     = rd.rolename
ORDER BY rc.rolename;

```

02) You can run this query and find out the authorizations. You give your oprid 
to find out which all pages you can see or put specific page name , here 
page name actually takes the pageitem name PNLITEMNAME which is page's name 
inside component when you open component
```
SELECT DISTINCT A.OPRID 
, E.PNLGRPNAME 
, D.MENUNAME 
, D.BARNAME 
, D.BARITEMNAME 
, D.PNLITEMNAME 
, E.SUBITEMNUM 
, D.DISPLAYONLY 
, D.AUTHORIZEDACTIONS 
FROM PSOPRDEFN A 
, PSROLECLASS B 
, PSROLEUSER C 
, PSAUTHITEM D 
, PS_SCC_COMPMENUBAR E 
WHERE B.ROLENAME = C.ROLENAME 
AND A.OPRID = C.ROLEUSER 
AND B.CLASSID = D.CLASSID 
AND D.MENUNAME = E.MENUNAME 
AND D.BARNAME = E.BARNAME 
AND D.BARITEMNAME = E.BARITEMNAME 
AND D.PNLITEMNAME = E.PNLITEMNAME 
AND A.OPRID='USER007'
```

03) How to see Name of RECORD Used Behind of any Page?
```
select distinct RECNAME
from PSPNLFIELD
where PNLNAME = 'LM_IN_DFLT_GEN'
```


# END
