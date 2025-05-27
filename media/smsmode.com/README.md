# Zabbix smsmode media type

## Overview

This type of Zabbix media allows SMS to be sent via the smsmode.com service

## Requirements

Zabbix version: 7.0 and higher.

## Parameters

After importing the webhook, you can configure it using webhook parameters.

### Configurable parameters

The configurable parameters are intended to be changed according to the webhook setup as well as the user's preferences and environment.

|Name|Value|Description|
|----|-----|-----------|
|apikey| | smsmode api key|


### Internal parameters

Internal parameters are reserved for predefined macros that are not meant to be changed.

|Name|Value|Description|
|----|-----|-----------|
| send_to | {ALERT.SENDTO} | Alert recipient | 
| message | {ALERT.MESSAGE} | Alert message | 



## smsmode setup


Go to the menu **Settings** => **System** => **API Keys** => **Generate a New API Key**


## Zabbix configuration

### Media type configuration

* 1 - Import media type into Zabbix
* 2 - Configure Variable **apikey**

### User Media Configuration

Add media to the user in the form +336000000000
