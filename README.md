# DLL-injection-attack-detection-using-Wazuh
A cybersecurity lab project demonstrating DLL Injection attack detection using Wazuh, Sysmon, and MITRE ATT&amp;CK T1055.001 techniques.

## Introduction

Dynamic-Link Library (DLL) injection is a process injection technique used by attackers to execute malicious code within the address space of another process. This technique is commonly associated with privilege escalation and defense evasion activities and is categorized under MITRE ATT&CK technique T1055.001.

This project demonstrates how Wazuh and Sysmon can be used to detect DLL injection activity in a Windows environment. A controlled lab environment was created to simulate the attack using InjectProc, while Sysmon generated the corresponding events and Wazuh correlated and generated alerts based on the observed behavior.

## Lab Environment

| Component        | Description            |
| ---------------- | ---------------------- |
| Wazuh Server     | Version 4.5.4          |
| Wazuh Agent      | Windows Agent 4.5.4    |
| Operating System | Windows 10             |
| Sysmon           | Sysinternals Sysmon    |
| Attack Tool      | InjectProc             |
| Payload          | hello-world-x64.dll    |
| Technique        | MITRE ATT&CK T1055.001 |

## Architecture

Attacker / Victim Machine (Windows) -> InjectProc.exe + hello-world-x64.dll -> Sysmon Event Logs -> Wazuh Agent -> Wazuh Manager -> Wazuh Dashboard Alert

## Prerequisites

Before performing the attack simulation, the following components were installed and configured:

* Microsoft Visual C++ Redistributable
* InjectProc
* hello-world-x64.dll
* Sysmon
* Wazuh Agent
* Wazuh Manager 4.5.4

## Installation & Configuration

This section describes the step-by-step setup of the lab environment used to simulate and detect DLL Injection attacks.

---

### 1. Microsoft Visual C++ Redistributable

The required Visual C++ runtime was installed on the Windows endpoint to ensure proper execution of InjectProc and related binaries.

![Download Visual C++](https://github.com/rabbi13/DLL-injection-attack-detection-using-Wazuh.-/blob/main/images/Download%20MS%20Visual%20C++.JPG?raw=true)
---
![images/Install MS Visual C++.JPG](https://github.com/rabbi13/DLL-injection-attack-detection-using-Wazuh.-/blob/2f553f616d48ac5c3a320b0bf6c328922577eed6/images/Install%20MS%20Visual%20C%2B%2B.JPG)
---

### 2. Downloading InjectProc Tool

The InjectProc tool and payload (`hello-world-x64.dll`) were downloaded and placed in a dedicated working directory.

📁 Directory structure:

C:\Users\fazleh\Downloads\DLLInjection\InjectProc.exe 

![Download InjectProc.exe](https://github.com/rabbi13/DLL-injection-attack-detection-using-Wazuh.-/blob/1e4e8b7b021f60d8a74afc5e0508f43b4ee7cd9d/images/Download%20InjectProc.JPG)
---
C:\Users\fazleh\Downloads\DLLInjection\hello-world-x64.dll ![Download hello-world-x64.dll](https://github.com/rabbi13/DLL-injection-attack-detection-using-Wazuh.-/blob/1e4e8b7b021f60d8a74afc5e0508f43b4ee7cd9d/images/Download%20Hellow%20world%20ddl.JPG)

---
C:\Users\fazleh\Downloads\DLLInjection\
![Dowload File](https://github.com/rabbi13/DLL-injection-attack-detection-using-Wazuh.-/blob/1e4e8b7b021f60d8a74afc5e0508f43b4ee7cd9d/images/Download%20Hello%20world%20ddl.JPG)


### 3. Wazuh Agent Configuration

The Wazuh agent was installed on the Windows endpoint and successfully connected to the Wazuh manager.
![Wazuh agent installation from manager](https://github.com/rabbi13/DLL-injection-attack-detection-using-Wazuh.-/blob/1e4e8b7b021f60d8a74afc5e0508f43b4ee7cd9d/images/Wazuh%20agent%20deploy%20process.jpeg) 

---
![Wazuh agent installation endpoint](https://github.com/rabbi13/DLL-injection-attack-detection-using-Wazuh.-/blob/1e4e8b7b021f60d8a74afc5e0508f43b4ee7cd9d/images/Wazuh%20agent%20active%20endpoint.JPG)

---
![Wazuh agent installation check in manager](https://github.com/rabbi13/DLL-injection-attack-detection-using-Wazuh.-/blob/1e4e8b7b021f60d8a74afc5e0508f43b4ee7cd9d/images/Wazuh%20agent%20active.JPG)

---
### 4. Sysmon Installation

Sysmon was installed using Sysinternals [page](https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon)  Sysmon tool with a predefined configuration file to monitor system activity, including process creation and remote thread injection events.

Command used:

```powershell
.\sysmon64.exe -accepteula -i sysmonconfig.xml
Get-Service sysmon64
```

![sysmon install and verify](https://github.com/rabbi13/DLL-injection-attack-detection-using-Wazuh.-/blob/0b8319759e37ca08998e81976f275c3270d58e9e/images/sysmon%20install%26verify.JPG)

### 5. Wazuh Sysmon Log Integration

Sysmon logs were integrated into Wazuh by modifying the agent configuration file (ossec.conf).Restart the Wazuh agent to apply the changes.

```Configuration added:

<localfile>
  <location>Microsoft-Windows-Sysmon/Operational</location>
  <log_format>eventchannel</log_format>
</localfile>
```
![Wazuh agent ossec.conf file edit](https://github.com/rabbi13/DLL-injection-attack-detection-using-Wazuh.-/blob/0b8319759e37ca08998e81976f275c3270d58e9e/images/wazuh%20agent%20ossec%20file%20edit%20for%20sysmon%20log.JPG)

![wazuh agent restart](https://github.com/rabbi13/DLL-injection-attack-detection-using-Wazuh.-/blob/914399460cb410653f5d5bd252ce4b346736acbf/images/wazuh%20agent%20restart%20after%20edit.JPG)

### 6. Wazuh Manager rules add
Add the following detection rules to the ``` /var/ossec/etc/rules/local_rules.xml ``` file on the Wazuh manager:

Command used:

```sudo nano /var/ossec/etc/rules/local_rules.xml ```

On that file add bellow configuration for get log from agent by the sysmon:

```
<group name="windows,sysmon">
  <rule id="100200" level="12">
    <if_sid>61610</if_sid>
    <description>Possible process injection activity detected from "$(win.eventdata.sourceImage)" on "$(win.eventdata.targetImage)"</description>
    <mitre>
      <id>T1055.001</id>
    </mitre>
  </rule>
  <rule id="100100" level="0">
    <if_sid>100200</if_sid>
    <field name="win.eventdata.sourceImage" type="pcre2">(C:\\\\Windows\\\\system32)|chrome.exe</field>
    <description>Ignore Windows binaries and Chrome</description>
  </rule>
</group>
```
![wazuh manager rules add](https://github.com/rabbi13/DLL-injection-attack-detection-using-Wazuh.-/blob/0b8319759e37ca08998e81976f275c3270d58e9e/images/add%20rules%20in%20wazuh%20manager.JPG)

### Attack Simulation

After completing the setup, a DLL injection attack was executed using InjectProc to simulate malicious process injection behavior.

Attack Command:
```
InjectProc.exe dll_inj hello-world-x64.dll cmd.exe
```
![Attack Simulation](https://github.com/rabbi13/DLL-injection-attack-detection-using-Wazuh.-/blob/914399460cb410653f5d5bd252ce4b346736acbf/images/inject%20ddl.JPG)


### Event Analysis (Sysmon)

Sysmon generated Event ID 8 (CreateRemoteThread), which is a strong indicator of process injection.

Key fields observed:

* Source Process: InjectProc.exe
* Target Process: cmd.exe
* Technique: LoadLibraryW injection
* Start Function: LoadLibraryW
![eventviewer sysmon alert](https://github.com/rabbi13/DLL-injection-attack-detection-using-Wazuh.-/blob/914399460cb410653f5d5bd252ce4b346736acbf/images/result%20in%20Eventviewer.JPG)


### Wazuh Detection

Wazuh successfully detected the malicious behavior using built-in rules and generated an alert.

* Rule ID: 100200
* Level: 12
* MITRE Technique: T1055.001 (DLL Injection)
* Technique Name:  Dynamic-link Library Injection
* Tactics:  Defense Evasion, Privilege Escalation

![wazuh-manager alert result](https://github.com/rabbi13/DLL-injection-attack-detection-using-Wazuh.-/blob/914399460cb410653f5d5bd252ce4b346736acbf/images/wazuh%20deshboar%20event%20chack.JPG)

### Key Findings
* DLL injection activity generated Sysmon Event ID 8.
* Wazuh successfully analyzed the Sysmon telemetry.
* Process injection behavior was detected and classified.
* Wazuh provided visibility into defense evasion and privilege escalation techniques.

### Conclusion

This lab demonstrated how DLL Injection attacks can be detected using Sysmon for telemetry generation and Wazuh for log analysis and alerting. The implementation successfully mapped malicious behavior to MITRE ATT&CK framework, providing visibility into process injection techniques in a Windows environment.


### References
* Wazuh [Documentation](https://wazuh.com/blog/detecting-process-injection-attacks-with-wazuh/)
* Microsoft Sysinternals Sysmon
* MITRE ATT&CK Framework
* Wazuh Blog – Detecting Process Injection Attacks with Wazuh
