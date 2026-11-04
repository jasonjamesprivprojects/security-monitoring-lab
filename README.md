
# Security Monitoring Lab

A personal lab for collecting and investigating security events from Windows and Ubuntu using Wazuh, with network traffic analysis in Wireshark.

## Objectives

- Collect security events from Windows and Linux endpoints.
- Verify file creation, modification and deletion detection.
- Investigate failed authentication attempts.
- Capture and interpret ICMP and SSH traffic.
- Document configuration, evidence and troubleshooting.

## Environment

| Component | Purpose |
|---|---|
| Windows 10 host | Monitored Windows endpoint and Wireshark workstation |
| VirtualBox | Runs the Wazuh server and Ubuntu endpoint |
| Wazuh 4.14.9 | Central event collection, analysis and dashboard |
| Ubuntu Server 24.04 LTS | Monitored Linux endpoint |
| Wireshark and Npcap | Network packet capture and analysis |

The Ubuntu endpoint uses a NAT adapter for internet access and a host-only adapter for lab communication. The Wazuh server is accessible through the host-only network.

## Validated Tests

| Test | Action | Observed result |
|---|---|---|
| Windows file monitoring | Created, modified and deleted files in `C:\WazuhLab` | Corresponding file integrity events appeared in Wazuh |
| Windows authentication failure | Attempted to launch a process using a nonexistent local account | Windows Event ID 4625 and Wazuh rule 60122 |
| Linux authentication failure | Entered an incorrect password for `sudo ls /root` | Wazuh rules 5503 and 5401; event details identified `labuser`, the command and an incorrect password attempt |
| ICMP traffic | Pinged the Ubuntu endpoint from Windows | Echo requests and replies captured in Wireshark |
| SSH traffic | Connected from Windows to Ubuntu over SSH | TCP connection establishment and SSH traffic captured |

## Investigation Approach

For each authentication test, I checked the endpoint, timestamp, attempted username, failure details and relevant command where available.

These were controlled tests. A failed authentication event alone does not establish malicious activity. File integrity alerts identify changes; further context is needed to determine whether those changes were authorized.

Packet captures provide network evidence. SSH encrypts session contents, so packet inspection does not reveal the password or commands entered after encryption begins.

## Troubleshooting Experience

- Investigated Ubuntu boot stalls and identified VirtualBox's Hyper-V compatibility mode in its log.
- Resolved DNS lookup failures during Ubuntu installation.
- Investigated a Wazuh manager startup timeout and restored service.
- Corrected a package URL typo that produced a download error.
- Replaced a failed `runas` password-input method with PowerShell credential-based process creation, then verified the resulting Windows security event.

## Limitations

- This is a small local lab, not a production security deployment.
- Detection tests use built-in Wazuh rules; custom detection engineering is not yet included.
- The tests demonstrate selected behaviors, not comprehensive threat coverage.
- Wireshark visibility depends on the selected capture interface.
- Windows 10 reflects the available lab host rather than a production operating-system recommendation.

## Evidence
Screenshots

## Detection Evidence

### Windows file integrity monitoring
Detected a file modification and the creation and deletion of a test file.

![File modified](screenshots/windows-file-modified.png)
![File added and deleted](screenshots/windows-file-added-deleted.png)

### Windows failed login
Detected a simulated failed login using the nonexistent account wazuhtest. Windows recorded Event ID 4625.

![Failed login alert](screenshots/windows-failed-login.png)
![Failed login details](screenshots/windows-failed-login-details.png)

### Linux failed sudo authentication
Detected an incorrect password attempt when labuser tried to run sudo ls /root on ubuntu-endpoint.

![Linux sudo failure details](screenshots/linux-sudo-failure-details.png)

## Learning Process
The test results were generated and checked in my own lab.
