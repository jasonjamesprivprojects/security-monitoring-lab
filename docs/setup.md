# Lab Setup and Validation

## Architecture
- Windows 10 physical host: VirtualBox, Wireshark, and Wazuh agent.
- Wazuh server VM: central monitoring and dashboard.
- Ubuntu Server VM: Linux endpoint with Wazuh agent.
- VirtualBox host-only network: communication between the host and VMs.
- NAT adapter: internet access for Ubuntu package downloads.

Lab addresses during testing:
- Wazuh server: 192.168.56.104
- Ubuntu endpoint: 192.168.56.105

Addresses may change after a restart. Confirm them before connecting.

## 1. Prepare the virtual machines
Import the Wazuh virtual appliance into VirtualBox.

Create a separate Ubuntu Server VM and install Ubuntu Server. Connect both VMs to the same host-only network. Give Ubuntu an additional NAT adapter for internet access.

Confirm that the Wazuh dashboard is accessible from the Windows host.

## 2. Connect the endpoints
Install the Windows Wazuh agent on the physical Windows host and configure it to connect to the Wazuh server.

Install the Ubuntu Wazuh agent and configure it with:
- Manager address: 192.168.56.104
- Agent name: ubuntu-endpoint

Enable and start the Linux agent:

sudo systemctl enable --now wazuh-agent

Confirm that windows-host and ubuntu-endpoint appear as active in Wazuh.

Use an agent version compatible with the installed manager.

## 3. Configure Windows file integrity monitoring
Create the directory C:\WazuhLab.

Back up the agent configuration before editing:

C:\Program Files (x86)\ossec-agent\ossec.conf

Inside the existing syscheck section, add:

<directories check_all="yes" realtime="yes">C:\WazuhLab</directories>

Ensure syscheck is enabled, then restart the agent from administrator PowerShell:

Restart-Service WazuhSvc

Allow the initial scan to finish before changing test files.

## 4. Validate file monitoring
Create, modify, and delete disposable text files inside C:\WazuhLab.

In Wazuh, open File Integrity Monitoring and inspect the events. Match the agent name, file path, event action, and timestamp to the test.

## 5. Validate Windows authentication monitoring
Ensure Windows failure auditing for Logon is enabled.

In PowerShell, request credentials for a nonexistent local test account:

$labCredential = Get-Credential -UserName "$env:COMPUTERNAME\wazuhtest"

Enter a made-up password, then run:

Start-Process -FilePath "$env:WINDIR\System32\cmd.exe" -Credential $labCredential

The credential error is expected.

Check the local Windows Security log for Event ID 4625, then confirm the corresponding alert in Wazuh.

## 6. Validate Linux authentication monitoring
On Ubuntu, run:

sudo -k
sudo ls /root

Enter a deliberately incorrect password once, then press Ctrl+C to cancel.

In Wazuh, inspect the authentication failure events for ubuntu-endpoint. Check the username, requested command, and failure message.

## 7. Inspect network traffic
On Windows, capture the VirtualBox host-only interface in Wireshark. In this lab, its Windows alias was Ethernet 2.

Generate ICMP traffic:

ping -n 4 192.168.56.105

Inspect it using:

icmp && ip.addr == 192.168.56.105

Start a fresh SSH connection:

ssh labuser@192.168.56.105

Inspect it using:

tcp.port == 22 && ip.addr == 192.168.56.105

Identify the TCP handshake and subsequent SSH traffic.

## Evidence and reports
Screenshots are included in the repository and displayed in the README.

Analysis:
- ../reports/detection-investigation.md
- ../reports/network-analysis.md

## Scope
This documents a personal learning lab using built-in Wazuh rules. It does not represent a production deployment.
