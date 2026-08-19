# SOC Project 1: SIEM Log Monitoring & Brute-Force Detection (Splunk)

## Overview
Built a hands-on SIEM lab to practice log ingestion, detection engineering, and incident investigation — part of a 10-project SOC Analyst learning series.

## Environment
- **SIEM:** Splunk Enterprise (local instance)
- **Log source:** Windows 11 VM (VirtualBox) with Sysmon installed
- **Ingestion:** Splunk Universal Forwarder shipping two log channels to Splunk:
  - Microsoft-Windows-Sysmon/Operational (process creation, etc.)
  - Windows Security log (authentication events)

## What I built
1. Configured Sysmon on a Windows 11 VM using the SwiftOnSecurity baseline config
2. Installed and configured Splunk Universal Forwarder to ship logs to Splunk running on the host machine
3. Simulated a brute-force login attempt (repeated failed logons)
4. Wrote an SPL detection query to flag the pattern:

    index=main EventCode=4625
    | bucket _time span=5m
    | stats count by _time, Account_Name
    | where count >= 3

5. Saved it as a scheduled Splunk Alert (cron: */5 * * * *), triggering per matching account — a persistent detection rule, not a one-off search

## Troubleshooting (the real part)
The forwarder initially failed to collect Sysmon events with errorCode=5 (Access Denied) in splunkd.log. Root cause: the Splunk Forwarder service was running under a restricted virtual service account (NT SERVICE\SplunkForwarder) that lacked permission to subscribe to the Sysmon event channel. Fixed by changing the service to run as Local System and restarting.

## Results
Detection query correctly identified 7 failed logon attempts across two accounts within a 5-minute window, matching the simulated attack.

## Screenshots

<img width="1786" height="1077" alt="Screenshot 2026-08-19 at 3 13 57 PM" src="https://github.com/user-attachments/assets/3e4dfdef-754c-4bed-8257-d7385a3b4a38" />

<img width="894" height="738" alt="Screenshot 2026-08-19 at 3 14 40 PM" src="https://github.com/user-attachments/assets/c7f94b28-08a4-4038-9279-d55d48211381" />

## Skills demonstrated
SIEM configuration, Windows event log analysis, Sysmon deployment, SPL query writing, detection engineering, Splunk alerting, Windows service permissions troubleshooting.
