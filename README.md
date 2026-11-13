# Wazuh SOC Lab & Incident Investigation Portfolio

An on-premise Security Operations Center (SOC) home lab built using **Wazuh SIEM**, **Windows**, and **Linux** endpoints. This repository documents my lab architecture, custom detection engineering, automated containment configurations, and incident investigation reports.

## Lab Architecture
* **SIEM / XDR:** Wazuh Manager, Indexer, and Dashboard
* **Endpoints Monitored:** 
  * Windows (10 Pro) VM 
  * Linux (Ubuntu) VM

## Incident Investigation Reports
| Report ID | Scenario | Target Endpoint | Key Detections & Tools |
| :--- | :--- | :--- | :--- | 
| **[IR-001](reports/IR-001-FIM-File-Modification.md)** | File Creation & Modification | Windows VM | Wazuh FIM (`syscheck`), Rules `554` & `550` |
| **[IR-002](reports/IR-002-Guest-Account-Enabled.md)** | Built-in Guest Account Enabled | Windows VM | Custom XML Rule, Windows Event ID '4722' |


## Repository Structure
* `/reports/` – Structured Incident Response reports with timelines, log telemetry, and remediation notes.
* `/screenshots/` – Dashboard alert captures and log verification evidence.
