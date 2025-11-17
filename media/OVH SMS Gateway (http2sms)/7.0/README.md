# OVH SMS Gateway (http2sms)

## Overview

This Zabbix media type allows sending SMS alerts using the OVH service via the HTTP2SMS method.

**Attention, if your host or alert contains a domain name, the SMS may not be delivered.**

## Requirements

  * Zabbix 7.0


## Parameters

After importing the webhook, you can configure it using webhook parameters.

### Configurable parameters

The configurable parameters are intended to be changed according to the webhook setup as well as the user's preferences and environment.

|Name|Value|Description|
|----|-----|-----------|
| ovh_login | <API Login> | Your api user login |
| ovh_password | <API Password>  | Your api user password |
| ovh_service | <SMS ID Service> | Your sms id |
| send_from | 0000000000 | Your from sender |


## OVH setup

### API User

Go to the menu  **SMS** => **Your SMS ID** =>  **Users** and create a new account 


## Zabbix configuration

### Media type configuration

* 1 - Import media type into Zabbix
* 2 - Configure Variable 
  * ovh_login
  * ovh_password
  * ovh_service
  * send_from (**Only if you have declared a Sender in your OVH account. If not, leave this field at its default value.**)
* 3 - If necessary, translate the messages in the **Message template** menu

### User Media Configuration

To configure the user media, use the following format (example for a French number)

```
+33612345678
```
