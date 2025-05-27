# smsmode.com by HTTP

## Overview

- This template is designed to monitor smsmode.com account
- It works without any external scripts and uses the HTTP agent item.

![image](https://github.com/user-attachments/assets/dfe347de-b390-434c-8009-ae427f1c567c)


## Requirements

Zabbix version: 7.0 and higher.


## Configuration

## smsmode.com setup 

Go to the menu Settings => System => API Keys => Generate a New API Key


### Macros used

|Name|Description|Default|
|----|-----------|-------|
|{$ACCOUNT.BALANCE.HIGH}|<p>Used to set the threshold in the alert currency</p>|2| 
|{$ACCOUNT.BALANCE.WARN}|<p>Used to set the threshold in the alert currency</p>|5|
|{$SMSMODE_API_KEY}|<p></p>||
|{$SMSMODE_MESSAGES_URL}|<p></p>|https://rest.smsmode.com/sms/v1/messages|
|{$SMSMODE_ORGANISATION_URL}|<p></p>|https://rest.smsmode.com/commons/v1/organisations|
|{$SMSMODE_PRICE}|<p></p>|0.0610|

### Items

|Name|Description|Type|Key and additional info| Preprocessing / Formula |
|----|-----------|----|-----------------------|-------------------------|
|smsmode: api status|<p>.</p>|HTTP Agent|smsmode.api.status| <ul><li><p>Check for not supported value => Set to value HTTP/1.1 000</p></li> <li><p>Regular expression `HTTP.*?(\d{3})` `\1`  </p></li></ul>|
|smsmode: get account|<p>.</p>|HTTP Agent|smsmode.get| - | 
|smsmode: get messages|<p>.</p>|HTTP Agent|smsmode.get.messages| - |
|smsmode: SMS available|<p>.</p>|Calculated|smsmode.sms.available| `last(//smsmode.balance.amount) / {$SMSMODE_PRICE}` |
|smsmode: Last messages|<p>.</p>|Calculated|smsmode.messages.status.cal| `count(last_foreach(/*/smsmode.messages.status[*]))` |
|smsmode: Last failed messages|<p>.</p>|Calculated|smsmode.messages.status.failed.cal| `sum(last_foreach(/*/smsmode.messages.status[*]))` |
|smsmode: Last failed messages %|<p>.</p>|Calculated|smsmode.messages.status.failed.percent|`(last(//smsmode.messages.status.failed.cal) * 100) / last(//smsmode.messages.status.cal)` |
|smsmode: account compagny|<p>.</p>|Dependent Item|smsmode.account.company.information|<ul><li><p>JSONPath: `$.items.[*].companyInformation.name.first()`</p></li> <li><p>Discard unchanged with heartbeat </p></li></ul>|
|smsmode: account state|<p>.</p>|Dependent Item|smsmode.account.state|<ul><li><p>JSONPath: `$.items.[*].state.first()`</p></li> <li><p>REPLACE: ACTIVATED => 1</p></li></ul>|
|smsmode: balance amount|<p>.</p>|Dependent Item|smsmode.balance.amount|<ul><li><p>JSONPath: `$.items.[*].balance.amount.first()`</p></li> </ul>|
|smsmode: monthly consumption|<p>.</p>|Dependent Item|smsmode.monthly.consumption|<ul><li><p>JSONPath: `$.items.[*].monthlyConsumption.first()`</p></li> </ul>|
|smsmode: monthly consumption limit|<p>.</p>|Dependent Item|smsmode.monthly.consumption.limit|<ul><li><p>JSONPath: `$.items.[*].monthlyConsumptionLimit.first()`</p></li> </ul>|



### Triggers

|Name|Description|Expression|Severity|Dependencies|
|----|-----------|----------|--------|--------------------------------|
|Insufficient account balance</p>||`last(/smsmode by HTTP/smsmode.balance.amount) <= 0.6`|Disaster|
|API unavailable</p>||`last(/smsmode by HTTP/smsmode.api.status) = 0`|Disaster|
|Account status: Not activated</p>||`last(/smsmode by HTTP/smsmode.account.state) <> 1`|Disaster|
|Account balance low < {$ACCOUNT.BALANCE.WARN}</p>||`last(/smsmode by HTTP/smsmode.balance.amount) <= {$ACCOUNT.BALANCE.WARN}`|Warning|smsmode by HTTP: Account balance low < {$ACCOUNT.BALANCE.HIGH}
|Account balance low < {$ACCOUNT.BALANCE.HIGH}</p>||`last(/smsmode by HTTP/smsmode.balance.amount) <= {$ACCOUNT.BALANCE.HIGH}`|High|smsmode by HTTP: Insufficient account balance

### LLD rule get message

|Name|Description|Type|Key and additional info|
|----|-----------|----|-----------------------|
|messages discovery|<p>get last message.</p>|Dependent item|smsmode.messages.discovery<p>**Preprocessing**</p><ul><li><p>JSONPath: `$.items[-10:]`</p></li></ul>|

### Item prototypes for Storage accounts discovery

|Name|Description|Type|Key and additional info| Preprocessing |
|----|-----------|----|-----------------------|----------------|
|{#MESSAGE_ID} : Message  Status|<p>.</p>|Dependent item|smsmode.messages.status[{{#MESSAGE_ID}]|<ul><li><p>JSONPath: `$.items[?(@.messageId == '{#MESSAGE_ID}')].status.value.first()`</p> <li><p>Replace SENT => 0 </p></li> <li><p>Replace UNDELIVERABLE => 1 </p></li> </li></ul>|
|{#MESSAGE_ID} : Message  status detail|<p>.</p>|Dependent item|smsmode.messages.status.detail[{{#MESSAGE_ID}]|<ul><li><p>JSONPath: `$.items[?(@.messageId == '{#MESSAGE_ID}')].status.detail.first()`</p> |

### Trigger prototypes for Storage accounts discovery

|Name|Description|Expression|Severity|Dependencies|
|----|-----------|----------|--------|--------------------------------|
|{#MESSAGE_ID} : Message  Status : undeliverable||`last(/smsmode by HTTP/smsmode.messages.status[{{#MESSAGE_ID}]) = 1`|Disaster|smsmode by HTTP: Insufficient account balance	|

