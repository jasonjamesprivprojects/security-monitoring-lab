# Detection Investigation Report

## Lab environment
Wazuh collected security events from a Windows host and an Ubuntu endpoint in a local VirtualBox lab.

## Test 1: Windows file changes
**Action:** Modified a monitored test file, then created and deleted another file in C:\WazuhLab.

**Observation:** Wazuh generated file integrity monitoring alerts for the changes.

**Analysis:** These alerts show changes to monitored files. A file change alone does not prove malicious activity. An investigation would check the file, timing, and whether the change was authorized.

**Outcome:** Confirmed that file integrity monitoring detected the controlled changes.

## Test 2: Windows failed login
**Action:** Attempted to start a process using the nonexistent local account wazuhtest.

**Observation:** Windows recorded Event ID 4625. Wazuh generated rule 60122: "Logon Failure - Unknown user or bad password."

**Analysis:** The event's substatus, 0xC0000064, indicated that the account did not exist. This was one controlled local attempt, so it does not demonstrate a brute-force attack.

**Outcome:** Confirmed that a Windows authentication failure reached Wazuh.

## Test 3: Linux failed sudo authentication
**Action:** Entered an incorrect password while attempting to run sudo ls /root as labuser.

**Observation:** Wazuh recorded failed authentication events. The expanded log identified labuser, the requested root account, the command /usr/bin/ls /root, and one incorrect password attempt.

**Analysis:** The event showed an unsuccessful attempt to authenticate for a privileged command. It did not show successful access to /root.

**Outcome:** Confirmed that Linux sudo authentication failures reached Wazuh.

## Conclusion
The lab demonstrated centralized visibility into Windows file changes and Windows and Linux authentication failures using built-in Wazuh rules.

All tests were controlled lab activity. No custom detection rules or automated responses were implemented.
