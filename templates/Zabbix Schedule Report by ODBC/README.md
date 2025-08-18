# Zabbix Schedule Report by ODBC

## Overview

- This template is designed to monitor Zabbix Scheduled Report


## Requirements

Zabbix version: 7.0 and higher.


## Configuration

### ODBC (MariaDB)

=> https://www.zabbix.com/documentation/current/en/manual/config/items/itemtypes/odbc_checks

/etc/odbcinst.ini

```
[MariaDB Unicode]
Driver=libmaodbc.so
Description=MariaDB Connector/ODBC(Unicode)
Threading=0
UsageCount=1
```

/etc/odbc.ini

```
[report]
Driver      = MariaDB Unicode
Server      = DB_IP
User        = USERNAME
Password    = USER_PASS
Port        = 3306
Database    = zabbix
```

### User

To ensure proper functionality, it is essential to create a dedicated user specifically for accessing the Zabbix database. 
This user must have read permissions on the report table to retrieve the necessary data. 

## Macros used

|Name|Description|Default|
|----|-----------|-------|
| {$DATABASE_NAME} |  |  | 
| {$DSN} |  |  | 


## Items

|Name|Description|Type|Key and additional info| Preprocessing / Formula |
|----|-----------|----|-----------------------|-------------------------|
| Get Report |<p>.</p>| Database monitor |  |


## Schedule Discovery

### Item prototypes 

|Name|Description|Type|Key and additional info| Preprocessing |
|----|-----------|----|-----------------------|----------------|
| Zabbix Report active since : {#REPORT_NAME} |<p>.</p>|Dependent item|zabbix.report.active_since[{#REPORT_ID}] |<ul><li><p> JSONPath: $[?(@.reportid == {#REPORT_ID})].active_since.first()</p></li>  <li><p>Discard unchanged with heartbeat: 12h   </p> </li> </ul>|
| Zabbix Report end date : {#REPORT_NAME}  | <p>.</p> | Dependent item|zabbix.report.active_till[{#REPORT_ID}] |  <ul><li><p> JSONPath: $[?(@.reportid == {#REPORT_ID})].active_till.first()</p></li>  <li><p>Discard unchanged with heartbeat: 12h   </p> </li> </ul>|
| Zabbix Report info : {#REPORT_NAME} | <p>.</p> | Dependent item|zabbix.report.info[{#REPORT_ID}] |   <ul><li><p> JSONPath: $[?(@.reportid == {#REPORT_ID})].info.first()</p></li>  <li><p>Discard unchanged with heartbeat: 12h   </p> </li> </ul>|
| Zabbix Report last sent : {#REPORT_NAME} | <p>.</p> | Dependent item|zabbix.report.lastsent[{#REPORT_ID}] |  <ul><li><p> JSONPath: $[?(@.reportid == {#REPORT_ID})].lastsent.first()</p></li>  <li><p>Discard unchanged with heartbeat: 12h   </p> </li> </ul>|
| Zabbix Report state : {#REPORT_NAME} | <p>.</p> | Dependent item|zabbix.report[{#REPORT_ID}] |  <ul><li><p> JSONPath: $[?(@.reportid == {#REPORT_ID})].state.first()</p></li>  <li><p>Discard unchanged with heartbeat: 12h   </p> </li> </ul>|
| Zabbix Report status : {#REPORT_NAME} | <p>.</p> | Dependent item|zabbix.status[{#REPORT_ID}] |  <ul><li><p> JSONPath: $[?(@.reportid == {#REPORT_ID})].status.first()</p></li>  <li><p>Discard unchanged with heartbeat: 12h   </p> </li> </ul>|

### Trigger prototypes

|Name|Description|Expression|Severity|Dependencies|
|----|-----------|----------|--------|--------------------------------|
|Zabbix Report expires : {#REPORT_NAME} ||`now() - last(/Zabbix Schedule Report by ODBC/zabbix.report.active_till[{#REPORT_ID}]) > 0 and last(/Zabbix Schedule Report by ODBC/zabbix.status[{#REPORT_ID}]) = 0`|Warning ||
|Zabbix Report state failed : {#REPORT_NAME} ||`last(/Zabbix Schedule Report by ODBC/zabbix.report[{#REPORT_ID}]) <> 1 and last(/Zabbix Schedule Report by ODBC/zabbix.status[{#REPORT_ID}]) = 0`|Disaster ||

