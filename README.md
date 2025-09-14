# soc-analyst-lab
SOC analyst lab showcasing blue team skills with Splunk SIEM, Sysmon(EDR), Wireshark, and Active Directory for log collection, threat detection, and 

## Overview

## Tools used
- Splunk (SIEM) 
- Wireshark
- Active Directory
- Sysmon (EDR)
- 
## Topology
- 3 Virtual Machines connected to eachother on host-only modes: Ubuntu(Splunk), Windows(Client), Windows Server(Domain Controller)
- Ubuntu configured to be Splunk Server on the web on port 8000.
- Windows Client configured with Splunk Universal Forwarder and Sysmon to send logs to Splunk consisting of Windows Event Logs and Process Creation. Wireshark used for packet sniffing to demonstrate logs on a network level.
- Windows Server configured with Active Directory, promoted to Domain Controller and the Client joins the Server. Password policy implemented for users in domain for better security. Also has Splunk Forwarder to send logs related to user and group changes, etc.

## Infrastructure Setup

## Detection and Analysis
