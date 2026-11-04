# Network Traffic Analysis

## Environment
Captured lab traffic on the Windows host using Wireshark's Ethernet 2 interface, connected to the VirtualBox host-only network.

Ubuntu endpoint address during testing: 192.168.56.105.

## Test 1: ICMP connectivity
From Windows, ran:

ping -n 4 192.168.56.105

Wireshark display filter:

icmp && ip.addr == 192.168.56.105

Observed ICMP Echo Requests and Echo Replies between Windows and Ubuntu.

This confirmed that the endpoint was reachable over the lab network at the time of testing.

## Test 2: SSH connection
From Windows, connected to Ubuntu using:

ssh labuser@192.168.56.105

Wireshark display filter:

tcp.port == 22 && ip.addr == 192.168.56.105

Observed the TCP three-way handshake: SYN, SYN-ACK, and ACK, followed by SSH traffic.

SSH encrypts session contents after key exchange. The capture allowed examination of connection metadata and protocol negotiation, rather than readable passwords or commands.

## Limitations
These were controlled connectivity tests, not evidence of malicious traffic.

Capture visibility depended on the selected interface. Display filters changed which packets were shown; they did not remove packets from the saved capture.

Packet capture files remain local and are not included in this repository.
