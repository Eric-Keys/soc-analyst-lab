# soc-analyst-lab
SOC analyst lab showcasing blue team skills with Splunk SIEM, Sysmon(EDR), Wireshark, and Active Directory for log collection, threat detection, and 

## Overview
This project demonstrates a hands-on SOC Analyst Lab designed to simulate a small enterprise environment and prctice blue team cybersecurity skills. It combines many in-demand tools to provide realistic scenarios for log collection, event correlation, and threat detection. This project helped me gain experience in incident monitoring and investigation.

## Tools used
- Splunk (SIEM) 
- Wireshark
- Active Directory
- Sysmon (EDR)
  
## Topology
- 3 Virtual Machines connected to eachother on host-only modes: Ubuntu(Splunk), Windows(Client), Windows Server(Domain Controller)
- Ubuntu configured to be Splunk Server on the web on port 8000.
- Windows Client configured with Splunk Universal Forwarder and Sysmon to send logs to Splunk consisting of Windows Event Logs and Process Creation. Wireshark used for packet sniffing to demonstrate logs on a network level.
- Windows Server configured with Active Directory, promoted to Domain Controller and the Client joins the Server. Password policy implemented for users in domain for better security. Also has Splunk Forwarder to send logs related to user and group changes, etc.

## Infrastructure Setup
1. Configured Virtual Machines and set up three machines: Ubuntu, Windows 10, and Windows Server. Each was placed on a host-only private network to ensure isolated communication. Connectivity was validated using ping tests across the machines.

2. On the Windows client, installed Sysmon via the Command Prompt [sysmon64.exe -accepteula -i sysmonconfig-export.xml]. This provided process creation, network connections, etc for Splunk ingestion.

3. Deployed Splunk on Ubuntu by mounting the Splunk ISO, copied the package into the VM, and installed it using [>sudo mkdir /mnt/cdrom >sudo mount /dev/sr0 /mnt/cdrom >cp /mnt/cdrom/splunk-*.deb ~/ >sudo dpkg -i splunk-*.deb]. Configured Splunk Enterprise as the SIEM for log collection and analysis.

4. Accessed Splunk Web and connected to the Splunk web interface at http://:8000. Created two custom indexes:wineventlog- for Windows Security/System/Application events | Sysmon- for Sysmon operational logs

5. Installed the Splunk Universal Forwarder on the Windows client. Updated inputs.conf to forward Sysmon and Windows Event Logs into Splunk on port 9997. Confirmed ingestion by verifying events appeared in the correct indexes.

6. Installed Active Directory Domain Services (AD DS) on the Windows Server and promoted it to a Domain Controller (lab.local). This provided centralized authentication and policy management.

7. Added two test accounts in AD and enforced password reset upon first login to simulate realistic security practices.

8. Configured static IPv4 addresses for both the Splunk server and the Domain Controller (based on assigned DHCP addresses). This ensured consistent connectivity within the lab environment.

9. Created two AD groups: ITAdmins and Sales. Assigned one test user to each group to model role-based access.

10. Applied stricter password requirements using a Group Policy Object (GPO). Policies were enforced through gpmc.msc by navigating through computer configurations all the way to password policy. This was done to simulate account hardening and generated logs for monitoring.

11. Joined the Windows client to the new domain, enabling centralized logon/authentication.

12. Logged into the domain with a test user account (“Alice”). The enforced GPO required a password change at first login, confirming domain and policy functionality.

13. Confirmed logs were flowing from both the Windows client and Domain Controller into Splunk indexes (wineventlog, sysmon). At this point, data pipelines were live for analysis.

## Detection and Analysis
1. Built a Splunk query to detect repeated failed logons. This highlights potential brute force or password spray attempts targeting the client machine.

2. Created a search for successful logons, primarily useful on the Domain Controller where central authentication occurs. This dataset contained 1,000+ records and provided baselines for normal activity.

3. Implemented queries for account creation/deletion and group membership changes. These events help detect privilege escalation or insider threat activity.

4. Configured Splunk to monitor suspicious process launches (e.g. PowerShell). This use case detects potential malicious tools or script execution.

5. Added a search for unusual outbound network connections from the Windows client. This helps detect possible command-and-control (C2) or data exfiltration activity.

6. Leveraged Splunk’s query generation features to format raw event logs into structured dashboards. This improved readability and made it easier to analyze authentication attempts, process activity, and network events.

7. Captured outbound network connections from endpoints, including process name, destination IP, and port. For example, EventCode 3 logs showed port 53 DNS traffic, which is typically normal but could also indicate DNS tunneling or C2 communication. Combined with failed logons, these logs help identify brute force attempts and potential data exfiltration.

8. Tracked process launches and their executable images. This data helps identify malicious scripts and suspicious executables running on endpoints. It also provides valuable parent/child process relationships for investigations.

9. Monitored account and group management on the Domain Controller. These events are linked to MITRE ATT&CK T1098 (Account Manipulation) and help detect privilege escalation, unauthorized user creation, or changes in group membership.

10. Validated Kerberos packet captures between the Windows client and Domain Controller. These packets contain ticket requests used for authentication. If abused, attackers can request tickets and attempt offline password cracking. Monitoring these packets demonstrates awareness of credential theft attack paths.

11. Observed CLDAP queries made by clients when locating the Domain Controller. While legitimate, attackers often use these requests for reconnaissance to enumerate domain controllers and services. Highlighting CLDAP activity helps identify potential early-stage attacks.

12. Captured SMB traffic showing requests and responses during client-to-DC sessions. Since SMB can be exploited for lateral movement (e.g., pass-the-hash, file share abuse), monitoring this activity is critical for detecting adversaries moving inside the network.
