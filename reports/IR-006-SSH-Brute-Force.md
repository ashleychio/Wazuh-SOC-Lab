# Incident Investigation Report: IR-006

* **Title:** Automated SSH Brute Force Attack Detection (`Hydra`)
* **Date:** November 13, 2026
* **Source Host:** Kali Linux VM 
* **Target Host:** Linux VM / `ACHIO-Linux` 
* **Status:** Closed – Benign Lab Simulation


## 1. Executive Summary
On November 13, 2026, Wazuh detected a high-volume automated SSH brute-force attack targeting the Linux virtual machine. Investigation confirmed that the attack was executed from the Kali Linux VM using the `hydra` credential-testing tool against the `admin` account, generating hundreds of rapid authentication failures (`PAM` / `sshd`). This simulation demonstrates successful SIEM visibility into credential-access attempts and MITRE ATT&CK **T1110 (Brute Force)**.


## 2. Detection Configuration
To simulate an active credential-access threat, an automated password dictionary attack was launched from the Kali Linux VM against the Linux target's SSH service (`port 22`) using RockYou wordlist credentials:
```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt 192.168.124.132 ssh
```

## 3. Timeline of Events
* **2026-11-13 21:56:05** – Kali Linux initiated the `hydra` SSH brute-force attack against the Linux target IP (`192.168.124.132`).
* **2026-11-13 21:56:48** – Linux system journal logged a high frequency of authentication failures and invalid user attempts (`pam_unix(sshd:auth): check pass; user unknown` and `Failed password for invalid user admin`).
* **2026-11-13 21:57:11** – Wazuh triggered Rule `5710` (*"Attempt to login using a non-existent user"*) after aggregating multiple rapid attempts from the source IP.

## 4. Telemetry & Log Findings
* **Wazuh Rule ID:** `5710` (Level 5)
* **Rule Description:** `sshd: Attempt to login using a non-existent user`
* **Source Workstation:** Kali Linux VM
* **Target Workstation:** Linux VM
* **Target User:** Admin
* **Times Rule Fired:** 343

## 5. Remediation & Key Learnings
* **Resolution:** Verified that the brute-force traffic originated from the authorized internal Kali Linux VM during a controlled red/blue team exercise. No accounts were compromised as root/SSH passwords were secure.
* **Key Learnings:**
  * **Detecting Automated Tooling:** Tools like `Hydra` generate an immediate, high-frequency spike in authentication logs, where monitoring for repeated `Event ID` / log signatures can help spot automated credential stuffing instantly.
  * **Value of Active Response:** While rule `5710` provides visibility, pairing this telemetry with an automated containment rule (`firewall-drop`) instantly severs the connection and protects production infrastructure from password guessing.
