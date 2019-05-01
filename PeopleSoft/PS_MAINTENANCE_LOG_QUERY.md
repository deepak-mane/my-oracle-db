# PS_MAINTENANCE_LOG Query

## Check latest PUM applied Date
```
select * from ps_maintenance_log 
where DTTM_IMPORTED IS NOT NULL 
and DESCRLONG like '%PUM%'
order by DTTM_IMPORTED DESC;
```
