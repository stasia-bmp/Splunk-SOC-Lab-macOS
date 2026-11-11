# Splunk SOC Lab (macOS)

## Overview

SIEM lab for the purpose of analyzing suspicious behavior in the area of authentication activity in macOS. A brute-force attack was simulated by me, and then a detection rule was created based on the logs in Splunk.

**Stack:** Splunk Enterprise (Free License), macOS Unified Logging (`logd` input), OpenSSH.

## 1. Log Collection

macOS does not write SSH login records to any flat-file log. Instead, these are recorded in Unified Logging. I made use of "logd" input of Splunk which allows reading data from it directly, filtering by process.

**Configuration of the input** (`/Applications/Splunk/etc/apps/logd_input/local/inputs.conf`):

```ini
[logd://ssh_events]
logd-process = sshd
logd-interval = 30
index = main
sourcetype = macos_ssh
disabled = 0
```

## 2. Attack simulation

To create realistic examples of suspicious activity in an ethical manner, I caused failed SSH logins on localhost by:
```
ssh test@localhost   # repeated with an incorrect password
```

## 3. Log analysis

I filtered logs with parameters "host" and "source"
<img width="2560" height="1370" alt="search by type" src="https://github.com/user-attachments/assets/8c6811dd-ca93-488c-adfd-2fd51c06136c" />

## 4. Alerting

The detection was saved in an alert schedule:

<img width="1610" height="1154" alt="Save as alert Splunk" src="https://github.com/user-attachments/assets/7a5db25c-6467-44a8-b394-3a8f19607368" />
<img width="2560" height="1370" alt="Save as alert Splunk brute" src="https://github.com/user-attachments/assets/2469e15f-bd27-4a08-a986-0433a147f351" />

## 5. Dashboard

And created a dashboard for good visualisation.
<img width="1656" height="363" alt="Failed login attempts_2026-11-11 at 03 05 16+0100_Splunk" src="https://github.com/user-attachments/assets/b5726154-8a40-47bb-950e-bc33c687582c" />

## Results:

MACOS SSH authentication events were successfully consumed by Splunk through the use of the "logd" modular input.

Diagnosed a field extraction issue and resolved it by switching to host/source.

Created a functional brute force detection rule mapped to the MITRE ATT&CK T1110.

Set up a scheduled alert and a dashboard for monitoring purposes.

Observed 3 failed logins against user test from the host Anastasiias-MacBook-Pro.local.
