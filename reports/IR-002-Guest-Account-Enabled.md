# Incident Investigation Report: IR-002

* **Title:** Built-in Guest Account Enabled
* **Date:** November 12, 2026
* **Target Host:** Windows VM
* **Status:** Closed - Benign Activity

## 1. Executive Summary
On November 12, 2026, Wazuh detected that the built-in `Guest` account was enabled on the Windows endpoint. Investigation confirmed that the account state change was performed by an authorised administrator (`AChio`) during normal operational hours as part of a controlled lab test, posing no threat to the environment.

## 2. Detection Configuration
To detect unauthorised activation of default Windows accounts, a custom XML detection rule was configured in `/var/ossec/etc/rules/local_rules.xml` on the Wazuh Manager. The rule monitors Windows Security **Event ID `4722`** (*"A user account was enabled"*) and triggers when the target account name matches `Guest`.

## 3. Timeline of Events
* **2026-11-12 10:55:08** - Windows Security Event ID `4722` logged: Built-in `Guest` account was enabled by user `AChio`.

## 4. Telemetry & Log Findings
* **Windows Event ID:** `4722` (A user account was enabled)
* **Wazuh Rule Triggered:** Custom Guest Account Alert (elevated from default Level 0 telemetry)
* **Subject / Actor Account:** `AChio`
* **Target Account:** `Guest`

## 5. Remediation & Key Learnings
* **Resolution:** Confirmed that `AChio` is an authorised administrator and verified that enabling the `Guest` account was an intentional, benign test. In a production environment, the `Guest` account would be disabled immediately if unauthorised.
* **Key Learnings:**
  * **Default Accounts:** Attackers frequently enable dormant built-in accounts like `Guest` (MITRE ATT&CK **T1078.001 – Valid Accounts: Default Accounts**) to maintain stealthy persistence without creating a brand-new user.
  * **Tuning Low-Level Telemetry:** By default, certain Windows account management logs arrive at Level 0 (informational) to prevent alert fatigue. Writing a targeted custom XML rule filtering specifically for `TargetUserName = Guest` ensures high-risk changes trigger actionable SOC alerts without flooding the dashboard.
