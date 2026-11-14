# Incident Investigation Report: IR-005

* **Title:** Network Reconnaissance & Port Scanning Detection (Nmap)
* **Date:** November 13, 2026
* **Source Host:** Kali Linux VM 
* **Target Host:** Windows VM 
* **Status:** Closed – Benign Lab Simulation

## 1. Executive Summary
On November 13, 2026, network reconnaissance activity was detected targeting the internal Windows lab virtual machine. Investigation confirmed that the scan was performed from the Kali Linux VM using `nmap` as part of a controlled purple team simulation by user `AChio`. The scan systematically initiated TCP connection attempts across multiple ports to map active network services on the target endpoint.

## 2. Detection Configuration
To simulate and capture network reconnaissance telemetry, an Nmap service version and targeted port scan was executed from the Kali Linux VM against the Windows VM's IP address:
```bash
nmap -sV -p 21,22,80,125,445,3389 <Windows-VM-IP>
```
![IR-005-Kali-Command](../screenshots/IR-005/IR-005-IR-005-KaliCommand.png)

## 3. Timeline of Events
* **2026-11-13 18:42:48** – Kali Linux initiated the `nmap` scan targeting the Windows VM IP address.
* **2026-11-13 18:42:52** – Windows Sysmon generated network connection events logged and ingested by Wazuh.

![IR-005-Wazuh-Log](../screenshots/IR-005/IR-005-IR-005-WazuhLog.png)

## 4. Telemetry & Log Findings
* **Technique:** Network Service Discovery (`T1046`)
* **Source IP (Attacker):** Kali Linux VM
* **Target IP (Victim):** Windows VM
* **Sysmon Event ID:** 3
* **Protocol:** TCP

![IR-005-Wazuh-Log-Details](../screenshots/IR-005/IR-005-IR-005-WazuhLogDetails.png)

## 5. Remediation & Key Learnings
* **Resolution:** Verified that the targeted port scan originated from the authorized internal Kali Linux VM during a controlled lab exercise. No exploitation attempt followed.
* **Key Learnings:**
  * **Early Indicators of Compromise:** Port scanning is almost always the initial precursor to an attack. Detecting these helps identify attackers mapping network perimeters before an exploit is launched (MITRE ATT&CK **T1046**).
  * **Interpreting Real-World Logs:** Real security logs show precise connection handshakes and port states rather than labeled "Nmap" alerts. Correlating specific service port access with external source IPs is how SOC analysts spot targeted reconnaissance.
