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



