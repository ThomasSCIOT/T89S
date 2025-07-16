# Proxmox Backup Server by HTTP

## Overview

- This template is designed to monitor Proxmox Backup Server
- It works without any external scripts and uses the HTTP agent item.

## Requirements

Zabbix version: 7.0 and higher.


## Configuration

## Proxmox Backup Server permissions




### Macros used

|Name|Description|Default|
|----|-----------|-------|
| {$PBS.CPU.PUSE.MAX.WARN} | Maximum used CPU in percentage. | 90 | 
| {$PBS.FAILED.TASKS.SINCE} | Tasks since in hours | 24  | 
| {$PBS.IP} | Proxmox Backup Server IP |  | 
| {$PBS.MEMORY.PUSE.MAX.WARN} | Maximum used memory in percentage. | 90 | 
| {$PBS.NODE.NAME} | Internal node name of PBS for API. Not used as https host. | localhost | 
| {$PBS.ROOT.PUSE.MAX.WARN} | Maximum used root space in percentage. | 90 | 
| {$PBS.STORAGE.PUSE.MAX.WARN} | Maximum used storage space in percentage. | 90 | 
| {$PBS.SWAP.PUSE.MAX.WARN} | Maximum used swap in percentage. | 90 | 
| {$PBS.TOKEN.ID} | API tokens allow stateless access to most parts of the REST API by another system, software or API client. | USER@REALM!TOKENID | 
| {$PBS.TOKEN.SECRET} | Secret key. | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | 
| {$PBS.URL.PORT} | The API uses the HTTPS protocol and the server listens to port 8007 by default. | 8007 | 

### Items

|Name|Description|Type|Key and additional info| Preprocessing / Formula |
|----|-----------|----|-----------------------|-------------------------|
| PBS: Version |<p>.</p>| HTTP Agent| | <ul><li> <p> JSONPath: $.data.version </p>|
| PBS: Get users |<p>.</p>| HTTP Agent | pbs.users |  |
| PBS: Get task verify |<p>.</p>| HTTP Agent | pbs.tasks.verify | |
| PBS: Running tasks |<p>.</p>| HTTP Agent | pbs.tasks.running | <ul><li> <p> JSONPath: $.total </p>|
| PBS: Get sync |<p>.</p>| HTTP Agent | pbs.sync | |
| PBS: Get subscription |<p>.</p>| HTTP Agent | pbs.subscription | |
| PBS: Get datastores usage | <p>.</p>| HTTP Agent | pbs.status.datastore-usage | <ul><li> <p> JSONPath: $.data </p> </li> |
| PBS: Get remote | <p>.</p> | HTTP Agent | pbs.remote |  |
| PBS: Get prune | <p>.</p> | HTTP Agent | pbs.prune |  |
| PBS: API Ping | <p>.</p> | HTTP Agent | pbs.ping | <ul><li> <p> JSONPath: $.data.pong </p> |
| PBS: Get node status | <p>.</p> | HTTP Agent | pbs.node.status | <ul><li> <p> JSONPath: $.data </p> </li> |
| PBS: Get garbage collect | <p>.</p> | HTTP Agent | pbs.gc | |
| PBS: Get prune | <p>.</p> | HTTP Agent | pbs.admin.prune | |
| PBS: Get failed tasks (last {$PBS.FAILED.TASKS.SINCE}h) |<p>.</p>|script| pbs.tasks.failed | |
| PBS : Users count |<p>.</p>| Dependent item | pbs.users.count | <ul><li> <p> JSONPath: $..userid.length() </p> |
| PBS: Verify task count | <p>.</p> | Dependent item | pbs.tasks.verify.count | <ul><li> <p> JSONPath: $..id.length() </p> |
| PBS: Sync task count |<p>.</p>| Dependent item | pbs.tasks.sync.count | <ul><li> <p> JSONPath: $..id.length() </p> </li> |
| PBS: Last failed verification error (last {$PBS.FAILED.TASKS.SINCE}h) |<p>.</p>|Dependent item| | <ul><li> <p> JSONPath: $.data[?(@.worker_type == "verificationjob")].status.first() </p> </li> <li><p> Discard Unchanged </p></li></ul>|
| PBS: Failed verification tasks (last {$PBS.FAILED.TASKS.SINCE}h) |<p>.</p>|Dependent item| pbs.tasks.failed.verificationjob | <ul><li> <p> JSONPath: $.data[?(@.worker_type == "verificationjob")].status.length() </p> </li> |
| PBS: Last failed sync error (last {$PBS.FAILED.TASKS.SINCE}h) |<p>.</p>|Dependent item| pbs.tasks.failed.syncjob.error | <ul><li> <p> JSONPath: $.data[?(@.worker_type == "syncjob")].status.first() </p> </li> <li><p> Discard Unchanged </p></li></ul>|
| PBS: Failed syncjob tasks (last {$PBS.FAILED.TASKS.SINCE}h) |<p>.</p>| Dependent item | pbs.tasks.failed.syncjob | <ul><li> <p> JSONPath: $.data[?(@.worker_type == "syncjob")].status.length() </p> </li> |
| PBS: Last failed reader error (last {$PBS.FAILED.TASKS.SINCE}h) |<p>.</p>|Dependent item| pbs.tasks.failed.reader.error | <ul><li> <p> JSONPath: $.data[?(@.worker_type == "reader")].status.first() </p> </li> Discard Unchanged <li><p>  </p></li></ul>|
| PBS: Failed reader tasks (last {$PBS.FAILED.TASKS.SINCE}h) | <p>.</p>|Dependent item| pbs.tasks.failed.reader | <ul><li> <p>JSONPath: $.data[?(@.worker_type == "reader")].status.length() </p> </li> |
| PBS: Last failed sync time (last {$PBS.FAILED.TASKS.SINCE}h) |<p>.</p>|Dependent item| pbs.tasks.failed.last_syncjob_time | <ul><li> <p> $.data[?(@.worker_type == "syncjob")].first() <p> Custom failed : Set value 0 <p> </p> </li> <li><p> JSONPath: $.endtime  </p></li> <li><p> JSONPath: $.endtime  <p> Custom failed : Set value 0 <p>  </p></li> <li><p> Discard Unchanged with heartbeat : 1h  </p></li></ul>|

