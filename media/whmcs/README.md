# Zabbix WHMCS Media type

## Overview

This guide describes how to integrate your Zabbix installation with WHMCS using the Zabbix webhook, and provides instructions for configuring a media type, user, and action in Zabbix.

This media type has two basic features essential for efficient alert management.

First, when an alert is triggered in Zabbix, a ticket is automatically opened in WHMCS. This action centralizes incident management and ensures rapid follow-up of any issues encountered.

Second, when the alert is no longer active in Zabbix, the corresponding ticket is automatically closed in WHMCS. This feature ensures that only relevant tickets remain open, facilitating resource management and incident tracking.

These two features ensure seamless integration between Zabbix and WHMCS, optimizing the responsiveness and efficiency of support teams.

## Tested on
 - WHMCS  8.12.1 

## Requirements

Zabbix version: 7.0 and higher.

## Parameters

After importing the webhook, you can configure it using webhook parameters.

### Configurable parameters

The configurable parameters are intended to be changed according to the webhook setup as well as the user's preferences and environment.

|Name|Value|Description|
|----|-----|-----------|
|whmcs_admin_username|\<PLACE WHMCS ADMIN Username\> | Use as display name in closing auto-response|
|whmcs_apiIdentifier|\<PLACE WHMCS API IDENTIFIER\>|Token API Identifier|
|whmcs_apiSecret|\<PLACE WHMCS API SECRET\>| Token API Secret|
|whmcs_departmentId|\<PLACE WHMCS departmentid\>| WHMCS Department ID for OpentTicket|
|whmcs_path_admin|\<PLACE WHMCS ADMIN PATH\>|WHMCS Admin PATH like /admin/ |
|whmcs_url|\<PLACE WHMCS URL\>|Current WHMCS URL.|


### Internal parameters

Internal parameters are reserved for predefined macros that are not meant to be changed.

|Name|Value|Description|
|----|-----|-----------|
|event_value|\{EVENT\.VALUE\}| Numeric value of the event that triggered an action (1 for problem, 0 for recovering).|
|trigger_value|\{TRIGGER\.VALUE\}| Returns the current trigger status as an integer (0 – ok, 1 – problem) |
| whmcs_clientId | {ALERT.SENDTO} | Alert recipient | 
| whmcs_message | {ALERT.MESSAGE} | Alert message | 
| whmcs_problem_id | {EVENT.TAGS.__zbx_whmcs_ticket_id} | WHMCS ticket ID |
| whmcs_subject | {ALERT.SUBJECT} | Alert subject |



## WHMCS setup

### API Restriction

Go to the menu **System Settings** => **System** => **General Settings** => **Security**

Add your IP in **API IP Access Restriction**

### API Role

Go to the menu **System Settings** => **API & Security** => **Manage API Credentials** 

Create a role with the permissions below

* GetTicket
* AddTicketReply
* OpenTicket
* UpdateTicket

### API User

Create a user with the previously created role

## Zabbix configuration

### Media type configuration

* 1 - Import media type into Zabbix
* 2 - Configure Variable 
  * whmcs_admin_username
  * whmcs_apiIdentifier
  * whmcs_apiSecret
  * whmcs_departmentId
  * whmcs_path_admin
  * whmcs_url
* 3 - If necessary, translate the messages that are currently in French in the **Message template** menu

### User Media Configuration

To configure the user media, use the following format:

```
ClientID;Email_CC
```

|Parameter|Type|Description|Required|
|----|----|-----------|-----------|
| ClientID | Int | the customer for whom the ticket will be opened | Required | 
| Email_CC | string | If you want to add a CC to the ticket, add the email address. Multiple emails can be added with the comma separator. | Optional |


* Example of client notification configuration with id 6

```
6
```

* Example of a customer notification configuration with ID 6 and CC mail@example.com


```
6;mail@example.com
```

* Example of client notification configuration with id 6 and CC mail@example.com and admin@example.com

```
6;mail@example.com,admin@example.com
```
