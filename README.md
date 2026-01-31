# Windows Privilege Escalation Lab

## Overview

This project documents a Windows privilege escalation lab focused on **service misconfigurations**. The goal is to demonstrate a clear, OSCP-aligned methodology for identifying privilege escalation opportunities on Windows systems **without immediately jumping to exploitation**.

The lab targets a realistic scenario where a low-privileged local user is able to identify a service running with elevated privileges and determine that its service binary directory is writable, leading to a valid privilege escalation condition.

This repository will be updated incrementally with screenshots and further write-ups as additional techniques are explored.

---

## Lab Environment

* **Operating System:** Windows 10
* **User Context:** Low-privileged local user (`FAHEEM\\ben`)
* **Access Level:** Interactive user, Medium Integrity
* **Focus Area:** Windows Services & File Permissions

---

## Objectives

* Practice manual Windows privilege escalation enumeration
* Understand how Windows services run under privileged contexts
* Identify insecure file permissions on service binaries
* Build clear, interview-ready documentation

---

## Service Enumeration & Permission Analysis

### Identify Current User Context

Initial enumeration was performed to understand the current privilege level and group memberships.

<img width="894" height="308" alt="image" src="https://github.com/user-attachments/assets/efa0d0a9-ee20-43fd-9e82-92f004e81398" />


### Enumerate Installed Services

Services were enumerated to identify potential targets for abuse.


<img width="614" height="39" alt="image" src="https://github.com/user-attachments/assets/ea3e35a1-9ada-4fb1-b969-dee9386a285d" />


This revealed a custom service running from a non-standard directory under `Program Files`, which warranted further inspection.

---

### Check Service Execution Context

The service configuration was queried to determine which account the service runs as.

<img width="535" height="184" alt="image" src="https://github.com/user-attachments/assets/6c77e07e-6c3d-493a-b64a-80a771a30b6b" />

The service is configured to run as **LocalSystem**. This is critical, as any code executed by this service would run with the highest level of privilege on the system.

---

### Inspect File and Directory Permissions

Since modifying service configuration was not possible as a low-privileged user, attention shifted to the **service binary directory permissions**.

Command used:

<img width="780" height="61" alt="image" src="https://github.com/user-attachments/assets/26a079cf-338a-4452-89be-c164dec46dfa" />


This indicates that the low-privileged user has **Full Control** over the directory containing the service executable.

---

### Enumerated all the services runnming from Progam Files

<img width="682" height="82" alt="image" src="https://github.com/user-attachments/assets/a37b111d-307b-4f03-bcd1-3fd8600a18a8" />

Filtered results to identify services with unquoted paths (paths containing spaces but no quotation marks):

<img width="621" height="77" alt="image" src="https://github.com/user-attachments/assets/9ff86e4c-aab2-4522-b07e-316a0852de0f" />

- The service binary path contains spaces
- The path is not enclosed in quotation marks
- This represents a potential unquoted service path vulnerability




