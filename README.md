# Windows Privilege Escalation via Weak Service Binary Permissions

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


### Assessing priviledge escalation
Token-based privilege escalation was assessed by enumerating assigned privileges and integrity level. The current user operates at Medium Integrity and does not possess SeImpersonatePrivilege or SeAssignPrimaryTokenPrivilege.

<img width="721" height="246" alt="image" src="https://github.com/user-attachments/assets/1393263f-f6ed-4826-9c06-66ee013570b5" />

This rules out token-based privilege escalation, shifting focus to service misconfiguration attacks.


## Verifying control permissions of changed binary

The original service executable was backed up and then removed to allow replacement. A benign payload (cmd.exe) was copied in its place, establishing control over code that will execute with the service’s SYSTEM privileges when started.

<img width="832" height="112" alt="image" src="https://github.com/user-attachments/assets/bdc98c07-3a9c-4fad-8044-380156d4dcda" />

This command was used to verify the access control permissions of the replaced service binary. The output confirms that the low-privileged user has Full Control over the executable, proving attacker control of code that will execute under the service’s SYSTEM context













