# Incident Investigation Report: IR-007

* **Title:** Automated Web Directory Enumeration & Reconnaissance (`Gobuster`)
* **Date:** November 13, 2026
* **Source Host:** Kali Linux VM 
* **Target Host:** Linux VM 
* **Status:** Closed – Benign Lab Simulation

## 1. Executive Summary
On November 13, 2026, Wazuh detected an automated web application reconnaissance attack targeting the Apache web server hosted on the internal Linux virtual machine. The Wazuh Manager aggregated and correlated the rapid sequence of HTTP 400-level errors from a single external IP address. Investigation confirmed that the activity was performed from the Kali Linux VM using the `gobuster` directory enumeration tool with the `common.txt` wordlist. This simulation demonstrates successful SIEM visibility into web server error log ingestion and MITRE ATT&CK **T1595.003 (Active Scanning: Wordlist Scanning)**.

## 2. Detection Configuration
To monitor web application traffic, the Linux endpoint's Wazuh agent (`ossec.conf`) was configured to ingest Apache access logs in real time:

To simulate discovery, a directory scan was executed from Kali against the target web server:
```bash
gobuster dir -u [http://192.168.124.132](http://192.168.124.132) -w /usr/share/wordlists/dirb/common.txt
```

## 3. Timeline of Events
- **2026-11-13 22:06:42** – Kali Linux initiated the `gobuster` directory scan targeting the Apache web server on `192.168.124.132`.
- **2026-11-13 22:06:46 - 22:06:47** – Apache access log recorded a high frequency of HTTP `404 Not Found` requests (`GET /xx HTTP/1.1`, `GET /xls HTTP/1.1`, etc.) originating from Kali IP `192.168.124.133` using User-Agent `gobuster/3.8.2`.
- **2026-11-13 22:06:46 - 22:06:47** – Wazuh triggered Rule `31151` (*"Multiple web server 400 error codes from same source ip"*) after correlating the rapid burst of error codes.

## 4. Telemetry & Log Findings
* **Wazuh Rule ID:** `31151` (Level 10)
* **Rule Description:** `Multiple web server 400 error codes from same source ip.`
* **Source Workstation:** Kali Linux VM
* **Target Workstation:** Linux VM
* **Times Rule Fired:** 141

## 5. Remediation & Key Learnings
* **Resolution:** Verified that the web enumeration scan originated from the authorised internal Kali Linux VM during a controlled lab exercise. No sensitive files or admin panels were exposed or compromised.
* **Key Learnings:**
  * **Detecting Website Mapping:** Tools like `Gobuster` automate the discovery of unlinked directories and hidden administrative files. Monitoring Apache access logs for rapid bursts of HTTP errors allows SOC analysts to detect attackers scouting for web vulnerabilities before exploitation occurs.
  * **Alert Correlation vs. Noise:** Single 404 errors are usually background noise, but using correlation rules like Wazuh Rule 31151, you avoid alert fatigue by grouping rapid error requests from the same IP into one serious alert instead of spamming your dashboard.
