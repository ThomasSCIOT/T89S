# Proxmox Backup Server by HTTP

## Overview

- This template is designed to monitor Proxmox Backup Server
- It works without any external scripts and uses the HTTP agent item.

## Requirements

Zabbix version: 7.0 and higher.


## Configuration

## Proxmox Backup Server permissions

Add API Token permission Audit to 
  * /datastore,
  * /system/status
  * /system/tasks
  * /remote
  * /system




## Macros used

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
| PBS: Last failed sync time (last {$PBS.FAILED.TASKS.SINCE}h) |<p>.</p>|Dependent item| pbs.tasks.failed.last_syncjob_time | <ul><li> <p> JSONPath: $.data[?(@.worker_type == "syncjob")].first() <p> Custom failed : Set value 0 <p> </p> </li> <li><p> JSONPath: $.endtime  </p></li> <li><p> JSONPath: $.endtime  <p> Custom failed : Set value 0 <p>  </p></li> <li><p> Discard Unchanged with heartbeat : 1h  </p></li></ul>|
| PBS: Last failed reader time (last {$PBS.FAILED.TASKS.SINCE}h) |<p>.</p>|Dependent item| pbs.tasks.failed.last_reader_time | <ul><li> <p> JSONPath: $.data[?(@.worker_type == "reader")].first() <p> Custom failed : Set value 0 <p> </p> </li> <li><p> JSONPath: $.endtime  </p></li> <li><p> JSONPath: $.endtime  <p> Custom failed : Set value 0 <p>  </p></li> <li><p> Discard Unchanged with heartbeat : 1h  </p></li></ul>|
| PBS: Last failed garbage time (last {$PBS.FAILED.TASKS.SINCE}h) |<p>.</p>|Dependent item| pbs.tasks.failed.last_garbage_collection_time | <ul><li> <p> JSONPath: $.data[?(@.worker_type == "garbage_collection")].first() <p> Custom failed : Set value 0 <p> </p> </li> <li><p> JSONPath: $.endtime  </p></li> <li><p> JSONPath: $.endtime  <p> Custom failed : Set value 0 <p>  </p></li> <li><p> Discard Unchanged with heartbeat : 1h  </p></li></ul>|
| PBS: Last failed backup time  (last {$PBS.FAILED.TASKS.SINCE}h) |<p>.</p>|Dependent item| pbs.tasks.failed.last_backup_time | <ul><li> <p> JSONPath: $.data[?(@.worker_type == "backup")].first() <p> Custom failed : Set value 0 <p> </p> </li> <li><p> JSONPath: $.endtime  </p></li> <li><p> JSONPath: $.endtime  <p> Custom failed : Set value 0 <p>  </p></li> <li><p> Discard Unchanged with heartbeat : 1h  </p></li></ul>|
| PBS: Last failed garbage error (last {$PBS.FAILED.TASKS.SINCE}h) |<p>.</p>|Dependent item| pbs.tasks.failed.garbage_collection.error | <ul><li> <p> JSONPath: $.data[?(@.worker_type == "garbage_collection")].status.first() <p> Custom failed : Set value 0 <p> </p> </li> <li><p> JSONPath: $.endtime  </p></li> <li><p> JSONPath: $.endtime  <p> Custom failed : Set value 0 <p>  </p></li> <li><p> Discard Unchanged with heartbeat : 1h  </p></li></ul>|
| PBS: Failed garbage tasks (last {$PBS.FAILED.TASKS.SINCE}h) |<p>.</p>|Dependent item| pbs.tasks.failed.garbage_collection | <ul><li> <p> JSONPath: $.data[?(@.worker_type == "garbage_collection")].status.length() <p> Custom failed : Set value 0 <p> </p> </li> <li><p> JSONPath: $.endtime  </p></li> <li><p> JSONPath: $.endtime  <p> Custom failed : Set value 0 <p>  </p></li> <li><p> Discard Unchanged with heartbeat : 1h  </p></li></ul>|
| PBS: Failed tasks (last {$PBS.FAILED.TASKS.SINCE}h) |<p>.</p>|Dependent item| pbs.tasks.failed.count | <ul><li> <p> JSONPath: $.data..status.length() <p> Custom failed : Set value 0 <p> </p> </li></ul>|
| PBS: Last failed backup error (last {$PBS.FAILED.TASKS.SINCE}h) |<p>.</p>|Dependent item| pbs.tasks.failed.backup.error | <ul><li> <p> $.data[?(@.worker_type == "backup")].status.first() <p> Custom failed : Discard value <p> </p> </li></ul>|
| PBS: Failed backup tasks {$PBS.FAILED.TASKS.SINCE}h |<p>.</p>|Dependent item| pbs.tasks.failed.backup | <ul><li> <p> JSONPath: $.data[?(@.worker_type == "backup")].status.length()  </p> </li></ul>|
| PBS : subscription status |<p>.</p>|Dependent item| pbs.subscription.status | <ul><li> <p> JSON: $..status.first() </p> </li> <li><p> Replace: notfound => 0  </p></li></ul>|
| PBS: Prune count |<p>.</p>|Dependent item|pbs.prune.count | <ul><li> <p> JSONPath: $..id.length() </p> </li></ul>|
| PBS Node: uptime |<p>.</p>|Dependent item| pbs.node.uptime | <ul><li> <p> JSONPath: $.uptime </p> </li></ul>|
| PBS Node: swap used |<p>.</p>|Dependent item| pbs.node.swap.used | <ul><li> <p> JSONPath: $.used</p> </li> <li><p>  </p></li></ul>|
| PBS Node: swap total |<p>.</p>|Dependent item| pbs.node.swap.total | <ul><li> <p> JSONPath: $.total</p> </li></ul>|
| PBS Node: swap free |<p>.</p>|Dependent item| pbs.node.swap.free | <ul><li> <p> JSONPath: $.free </p> </li></ul>|
| PBS Node: swap |<p>.</p>|Dependent item|pbs.node.swap | <ul><li> <p>JSONPath: $.swap </p> </li></ul>|
| PBS Node: root used |<p>.</p>|Dependent item| pbs.node.root.used | <ul><li> <p> JSONPath: $.used </p> </li> </ul>|
| PBS Node: root total |<p>.</p>|Dependent item| pbs.node.root.total | <ul><li> <p> JSONPath: $.total </p> </li></ul>|
| PBS Node: root avail |<p>.</p>|Dependent item| pbs.node.root.avail | <ul><li> <p> JSONPath: $.avail</p> </li></ul>|
| PBS Node: root |<p>.</p>|Dependent item| pbs.node.root | <ul><li> <p> JSONPath: $.root</p> </li></ul>|
| PBS Node: memory used |<p>.</p>|Dependent item| pbs.node.memory.used | <ul><li> <p> JSONPath: $.used </p> </li></ul>|
| PBS Node: memory total |<p>.</p>|Dependent item| pbs.node.memory.total | <ul><li> <p> JSONPath: $.total</p> </li></ul>|
| PBS Node: memory free |<p>.</p>|Dependent item| pbs.node.memory.free | <ul><li> <p> JSONPath: $.free </p> </li></ul>|
| PBS Node: memory |<p>.</p>|Dependent item| pbs.node.memory | <ul><li> <p> JSONPath: $.memory </p> </li></ul>|
| PBS Node: kernel |<p>.</p>|Dependent item| pbs.node.kversion | <ul><li> <p> JSONPath: $.kversion </p> </li></ul>|
| PBS Node: cpu usage |<p>.</p>|Dependent item| pbs.node.cpu | <ul><li> <p> JSONPath: $.cpu </p> </li> <li><p> Custom multiplier: 100  </p></li></ul>|
| PBS: gc store |<p>.</p>|Dependent item| | <ul><li> <p> JSONPath: $..store.first() <p> Custom failed : Discard value </p></p> </li> </ul>|
| PBS: gc still-bad |<p>.</p>|Dependent item| pbs.gc.still-bad | <ul><li> <p> JSONPath: $..['still-bad'].first() <p>  Custom failed : Discard value </p> </p> </li> </ul>|
| PBS: gc schedule |<p>.</p>|Dependent item| pbs.gc.schedule | <ul><li> <p> JSONPath: $..schedule.first() <p>  Custom failed : Discard value </p> </p> </li> </ul>|
| PBS: gc pending-bytes |<p>.</p>|Dependent item| pbs.gc.pending-bytes | <ul><li> <p> $..['pending-bytes'].first() <p>  Custom failed : Discard value </p> </p> </li> </ul>|
| PBS: gc next-run |<p>.</p>|Dependent item| pbs.gc.next-run | <ul><li> <p> $..['next-run'].first() <p>  Custom failed : Discard value </p> </p> </li> </ul>|
| PBS: gc disk-chunks |<p>.</p>|Dependent item| pbs.gc.disk-chunks | <ul><li> <p> $..['disk-chunks'].first() <p>  Custom failed : Discard value </p> </p> </li> </ul>|
| PBS: gc disk-bytes |<p>.</p>|Dependent item| pbs.gc.disk-bytes | <ul><li> <p> $..['disk-bytes'].first() <p>  Custom failed : Discard value </p> </p> </li> </ul>|
| PBS: Verify task successfull % |<p>.</p>|Calculated| pbs.tasks.verify.successfull.percent | <p>(last(//pbs.tasks.verify.successfull) * 100) / last(//pbs.tasks.verify.count) </p> |
| PBS: Verify task successfull |<p>.</p>|Calculated| pbs.tasks.verify.successfull | <p> sum(last_foreach(//pbs.verify.last-run-state[*])) </p> |
| PBS: sync task successfull % |<p>.</p>|Calculated| pbs.tasks.sync.successfull.percent | <p> (last(//pbs.sync.verify.successfull)*100) / last(//pbs.tasks.sync.count) </p> |
| PBS: Prune task successfull % |<p>.</p>|Calculated| pbs.tasks.prune.successfull.percent | <p>(last(//pbs.prune.verify.successfull)*100) / last(//pbs.prune.count) </p> |
| PBS: Sync task successfull |<p>.</p>|Calculated| pbs.sync.verify.successfull | <p> sum(last_foreach(//pbs.sync.last-run-state[*])) </p> |
| PBS: Prune task successfull |<p>.</p>|Calculated| pbs.prune.verify.successfull | <p> sum(last_foreach(//pbs.prune.last-run-state[*])) </p> |
| PBS Node: swap used percent |<p>.</p>|Calculated| pbs.node.swap.pused | <p> last(//pbs.node.swap.used) / last(//pbs.node.swap.total) * 100 </p> |
| PBS Node: root used percent |<p>.</p>|Calculated| pbs.node.root.pused | <p>last(//pbs.node.root.used) / last(//pbs.node.root.total) * 100 </p> |


### Triggers

|Name|Description|Expression|Severity|Dependencies|
|----|-----------|----------|--------|--------------------------------|
|<p>Users has changed</p>||`last(/Proxmox Backup Server by HTTP/pbs.users.count) <> last(/Proxmox Backup Server by HTTP/pbs.users.count,#2) `| High |
|<p>PBS: Node high swap usage</p>||`last(/Proxmox Backup Server by HTTP/pbs.node.swap.pused)>{$PBS.SWAP.PUSE.MAX.WARN} `| Average |
|<p>PBS: Node high root usage</p>||` last(/Proxmox Backup Server by HTTP/pbs.node.root.pused)>{$PBS.ROOT.PUSE.MAX.WARN} `| Average |
|<p>PBS: Node high memory usage</p>||`last(/Proxmox Backup Server by HTTP/pbs.node.memory.pused)>{$PBS.MEMORY.PUSE.MAX.WARN} `| Average |
|<p>PBS: Node high cpu usage</p>||`last(/Proxmox Backup Server by HTTP/pbs.node.cpu)>{$PBS.CPU.PUSE.MAX.WARN} `| Average |
|<p>PBS: API service not available</p>||`last(/Proxmox Backup Server by HTTP/pbs.ping)<>1 `| Disaster |
|<p>New sync failed tasks</p>||` last(/Proxmox Backup Server by HTTP/pbs.tasks.failed.syncjob) > 1 `| Disaster |
|<p>New garbage failed tasks</p>||`last(/Proxmox Backup Server by HTTP/pbs.tasks.failed.garbage_collection) > 1 `| Disaster |
|<p>New backup failed tasks</p>||` last(/Proxmox Backup Server by HTTP/pbs.tasks.failed.backup) > 1`| Disaster |

### LLD rule PBS Datastore discovery

#### Item prototypes 

|Name|Description|Type|Key and additional info| Preprocessing |
|----|-----------|----|-----------------------|----------------|
| PBS Datastore: {#DATASTORE} used percent |<p>.</p>|Calculated|pbs.datastore.pused[{#DATASTORE}] |<ul><li><p>last(//pbs.datastore.used[{#DATASTORE}]) / last(//pbs.datastore.total[{#DATASTORE}]) * 100</p></ul>|
| PBS Datastore: {#DATASTORE} |<p>.</p>|Dependent item|pbs.datastore[{#DATASTORE}] |<ul><li><p>JSONPath: `$[?(@.store == '{#DATASTORE}')]`</p> </li>  <li><p>JSONPath: `$.[0]`</p> </li> </ul>|
| PBS Datastore: {#DATASTORE} avail |<p>.</p>|Dependent item| pbs.datastore.avail[{#DATASTORE}]|<ul><li><p>JSONPath: `$.avail`</p></li> </ul> |
| PBS Datastore: {#DATASTORE} total |<p>.</p>|Dependent item| pbs.datastore.total[{#DATASTORE}] |<ul><li><p>JSONPath: `$.total`</p></li> </ul> |
| PBS Datastore: {#DATASTORE} used |<p>.</p>|Dependent item| pbs.datastore.used[{#DATASTORE}] |<ul><li><p>JSONPath: `$.used`</p></li> </ul> |


### Trigger prototypes

|Name|Description|Expression|Severity|Dependencies|
|----|-----------|----------|--------|--------------------------------|
|PBS Datastore: {#DATASTORE} high usage||`last(/Proxmox Backup Server by HTTP/pbs.datastore.pused[{#DATASTORE}])>{$PBS.STORAGE.PUSE.MAX.WARN}`|Average ||


### LLD rule PBS Prune configuration discovery

#### Item prototypes 

|Name|Description|Type|Key and additional info| Preprocessing |
|----|-----------|----|-----------------------|----------------|
| Prune {#PRUNE_ID} : Keep-weekly |<p>.</p>|Dependent item|pbs.prune.keep-weekly[{#PRUNE_ID}] |<ul><li><p> JSONPath: $.data[?(@.id == '{#PRUNE_ID}')].['keep-weekly'].first()   <p> Custom Failed : Set to value 0   </p> </p>   </li> </ul>|
| Prune {#PRUNE_ID} : schedule |<p>.</p>|Dependent item|pbs.prune.schedule[{#PRUNE_ID}] |<ul><li><p> JSONPath: $.data[?(@.id == '{#PRUNE_ID}')].schedule.first()</li> </ul>|
| Prune {#PRUNE_ID} : store |<p>.</p>|Dependent item|pbs.prune.store[{#PRUNE_ID}] |<ul><li><p> JSONPath: $.data[?(@.id == '{#PRUNE_ID}')].store.first()   </p>  </li> </ul>|
| Prune {#PRUNE_ID} : last-run-endtime |<p>.</p>|Dependent item|pbs.prune.last-run-endtime[{#PRUNE_ID}] |<ul><li><p> JSONPath $.data[?(@.id == '{#PRUNE_ID}')].['last-run-endtime'].first()   <p> Custom Failed : Set to value 0   </p> </p> </li>  </li> </ul>|
| Prune {#PRUNE_ID} : last-run-state |<p>.</p>|Dependent item|pbs.prune.last-run-state[{#PRUNE_ID}] |<ul><li><p> Check for not supported value : Any => Set to value 0   <p> Custom Failed : Set to value 0    </li> <li> <p> JSONPath: $.data[?(@.id == '{#PRUNE_ID}')].['last-run-state'].first() </p> <p>Custom failed 0 </p> </li> <li> <p>Replace : OK => 1 </p> </li></ul>|
| Prune {#PRUNE_ID} : Keep-last |<p>.</p>|Dependent item|pbs.prune.keep-last[{#PRUNE_ID}]|<ul><li><p> JSONPath $.data[?(@.id == '{#PRUNE_ID}')].['keep-last'].first()   <p> Custom Failed : Set to value 0   </p> </p> </li>  </li> </ul>|
| Prune {#PRUNE_ID} : Keep-monthly |<p>.</p>|Dependent item|pbs.prune.keep-monthly[{#PRUNE_ID}]|<ul><li><p> JSONPath $.data[?(@.id == '{#PRUNE_ID}')].['keep-monthly'].first()   <p> Custom Failed : Set to value 0   </p> </p> </li>  </li> </ul>|


### Trigger prototypes

|Name|Description|Expression|Severity|Dependencies|
|----|-----------|----------|--------|--------------------------------|
|Prune {#PRUNE_ID} : Keep-last has changed ||` last(/Proxmox Backup Server by HTTP/pbs.prune.keep-last[{#PRUNE_ID}]) <> last(/Proxmox Backup Server by HTTP/pbs.prune.keep-last[{#PRUNE_ID}],#2)`|Warning ||
|Prune {#PRUNE_ID} : Keep-monthly has changed ||`last(/Proxmox Backup Server by HTTP/pbs.prune.keep-monthly[{#PRUNE_ID}]) <> last(/Proxmox Backup Server by HTTP/pbs.prune.keep-monthly[{#PRUNE_ID}],#2) `|Warning ||
| Prune {#PRUNE_ID} : Keep-weekly has change||`last(/Proxmox Backup Server by HTTP/pbs.prune.keep-weekly[{#PRUNE_ID}]) <> last(/Proxmox Backup Server by HTTP/pbs.prune.keep-weekly[{#PRUNE_ID}],#2) `|Warning ||


### LLD rule PBS Sync discovery

#### Item prototypes 

|Name|Description|Type|Key and additional info| Preprocessing |
|----|-----------|----|-----------------------|----------------|
| PBS: Sync job {#SYNC_ID} next run |<p>.</p>|Dependent item| pbs.sync.next-run[{#SYNC_ID}]  |<ul><li><p> JSONPath $.data[?(@.id == '{#SYNC_ID}')].['next-run'].first() <p> Custom Failed : Discard value   </p> </p>   </li> </ul>|
| PBS: Sync job {#SYNC_ID} last-run-endtime |<p>.</p>|Dependent item| pbs.sync.last-run-endtime[{#SYNC_ID}]  |<ul><li><p> JSONPath $.data[?(@.id == '{#SYNC_ID}')].['last-run-endtime'].first() <p> Custom Failed : Discard value   </p> </p>   </li> </ul>|
| PBS: Sync job {#SYNC_ID} last-run-state |<p>.</p>|Dependent item| pbs.sync.last-run-state[{#SYNC_ID}]  |<ul><li><p> JSONPath $.data[?(@.id == '{#SYNC_ID}')].['last-run-state'].first() <p> Custom Failed : Discard value   </p> </p>   </li> <li><p> Regular Expression `^(?!OK$).*`  `0` </p> <p> Custom failed : Set to value 1 </p> </li> </ul>|
| PBS: Sync job {#SYNC_ID} schedule |<p>.</p>|Dependent item| pbs.sync.schedule[{#SYNC_ID}]  |<ul><li><p> JSONPath $.data[?(@.id == '{#SYNC_ID}')].['schedule'].first() <p> Custom Failed : Discard value   </p> </p>   </li> </ul>|
| PBS: Sync job {#SYNC_ID} ns |<p>.</p>|Dependent item| pbs.sync.ns[{#SYNC_ID}]  |<ul><li><p> JSONPath $.data[?(@.id == '{#SYNC_ID}')].['ns'].first() <p> Custom Failed : Discard value   </p> </p>   </li> </ul>|


### Trigger prototypes

|Name|Description|Expression|Severity|Dependencies|
|----|-----------|----------|--------|--------------------------------|
|PBS: Sync job {#SYNC_ID} failed ||` last(/Proxmox Backup Server by HTTP/pbs.sync.last-run-state[{#SYNC_ID}]) = 0`|Disaster ||



### LLD rule PBS Users discovery

#### Item prototypes 

|Name|Description|Type|Key and additional info| Preprocessing |
|----|-----------|----|-----------------------|----------------|
| PBS: user {#PBS_USERID} status |<p>.</p>|Dependent item| pbs.user.enable[{#PBS_USERID}]   |<ul><li><p> JSONPath : $.data[?(@.userid == '{#PBS_USERID}')].enable.first()  </p> </p> <p> Boolean to decimal </p>   </li> </ul>|
| PBS: user {#PBS_USERID} is expires |<p>.</p>|Dependent item| pbs.user.status[{#PBS_USERID}]   |<ul><li><p> JSONPath : $.data[?(@.userid == '{#PBS_USERID}')].expire.first()  </p> <p> Boolean to decimal </p>   </li> </ul>|


### Trigger prototypes

|Name|Description|Expression|Severity|Dependencies|
|----|-----------|----------|--------|--------------------------------|
|last(/Proxmox Backup Server by HTTP/pbs.user.enable[{#PBS_USERID}]) = 0||` PBS: user {#PBS_USERID} is disable`|Warning ||








### LLD rule PBS verify tasks discovery

#### Item prototypes 

|Name|Description|Type|Key and additional info| Preprocessing |
|----|-----------|----|-----------------------|----------------|
|Verify tasks: {#VERIFY_ID} schedule  |<p>.</p>|Dependent item|pbs.verify.schedule[{#VERIFY_ID}]   |<ul><li><p> JSONPath : $.data[?(@.id == '{#VERIFY_ID}')].schedule.first()  </p> </p> <p> Custom on failed : Discard value </p>  </li> </ul>|
| Verify tasks: {#VERIFY_ID} store |<p>.</p>|Dependent item| pbs.verify.store[{#VERIFY_ID}]  |<ul><li><p> JSONPath : $.data[?(@.id == '{#VERIFY_ID}')].store.first()  </p> </p> <p> Custom on failed : Discard value </p>  </li> </ul>|
| Verify tasks: {#VERIFY_ID} last run endtime |<p>.</p>|Dependent item| pbs.verify.last-run-endtime[{#VERIFY_ID}]  |<ul><li><p> JSONPath : $.data[?(@.id == '{#VERIFY_ID}')].['last-run-endtime'].first()  </p> </p> <p> Custom on failed : Discard value </p>  </li> </ul>|
| Verify tasks: {#VERIFY_ID} last run state |<p>.</p>|Dependent item| pbs.verify.last-run-state[{#VERIFY_ID}]  |<ul><li><p> JSONPath $.data[?(@.id == '{#VERIFY_ID}')].['last-run-state'].first() <p> Custom Failed : Discard value   </p> </p>   </li> <li><p> Regular Expression `^(?!OK$).*`  `0` </p> <p> Custom failed : Set to value 1 </p> </li> </ul>|
| Verify tasks: {#VERIFY_ID} next run |<p>.</p>|Dependent item|pbs.verify.next-run[{#VERIFY_ID}]   |<ul><li><p> JSONPath : $.data[?(@.id == '{#VERIFY_ID}')].['next-run'].first()  </p> </p> <p> Custom on failed : Discard value </p>  </li> </ul>|
| Verify tasks: {#VERIFY_ID} ns |<p>.</p>|Dependent item| pbs.verify.ns[{#VERIFY_ID}]  |<ul><li><p> JSONPath : $.data[?(@.id == '{#VERIFY_ID}')].ns.first()  </p> </p> <p> Custom on failed : Discard value </p>  </li> </ul>|
|  Verify tasks: {#VERIFY_ID} outdated-after|<p>.</p>|Dependent item| pbs.verify.outdated-after[{#VERIFY_ID}]  |<ul><li><p> JSONPath :  $.data[?(@.id == '{#VERIFY_ID}')].['outdated-after'].first() </p> </p> <p> Custom on failed : Discard value </p>  </li> </ul>|


### Trigger prototypes

|Name|Description|Expression|Severity|Dependencies|
|----|-----------|----------|--------|--------------------------------|
| Verify tasks: {#VERIFY_ID} last run endtime > 24h| |`fuzzytime(/Proxmox Backup Server by HTTP/pbs.verify.last-run-endtime[{#VERIFY_ID}],90000s) = 0 and last(/Proxmox Backup Server by HTTP/pbs.verify.last-run-state[{#VERIFY_ID}]) <> 0 `| Warning |  |
| Verify tasks: {#VERIFY_ID} last run failed | |` last(/Proxmox Backup Server by HTTP/pbs.verify.last-run-state[{#VERIFY_ID}]) <> 1 and last(/Proxmox Backup Server by HTTP/pbs.verify.last-run-endtime[{#VERIFY_ID}]) <> 0`| Average |  |
| Verify tasks: {#VERIFY_ID} ns has changed | |` last(/Proxmox Backup Server by HTTP/pbs.verify.ns[{#VERIFY_ID}]) <> last(/Proxmox Backup Server by HTTP/pbs.verify.ns[{#VERIFY_ID}],#2)`| Warning |  |
| Verify tasks: {#VERIFY_ID} schedule has changed | |`last(/Proxmox Backup Server by HTTP/pbs.verify.schedule[{#VERIFY_ID}]) <> last(/Proxmox Backup Server by HTTP/pbs.verify.schedule[{#VERIFY_ID}],#2) `| Warning |  |
| Verify tasks: {#VERIFY_ID} store has changed | |` last(/Proxmox Backup Server by HTTP/pbs.verify.store[{#VERIFY_ID}]) <> last(/Proxmox Backup Server by HTTP/pbs.verify.store[{#VERIFY_ID}],#2)`| Warning |  |







