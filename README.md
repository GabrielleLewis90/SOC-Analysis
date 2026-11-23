SOC Analysis Project
This project demonstrates a Security Operations Center (SOC) workflow using Wazuh SIEM to detect, investigate, and respond to security events on an Ubuntu endpoint. It covers SSH authentication monitoring, privileged command execution, File Integrity Monitoring (FIM), VirusTotal threat intelligence enrichment, automated response, and post-incident verification.

Table of Contents
- Executive Summary
- Environment
- Alert Detection and Investigation
- Malware Detection and Response with VirusTotal
- Detection and Response
- Threat Intelligence
- Results and Findings
- Response and Remediation
- Verification
- Conclusion
- Author

Executive Summary
The purpose of this project was to demonstrate practical SOC analyst skills through the deployment and configuration of Wazuh SIEM in a controlled virtual environment. The exercise involved generating security events, detecting and investigating alerts, integrating VirusTotal for threat intelligence enrichment, and implementing automated incident response.

The project objectives were to:
- Generate security events on an Ubuntu endpoint.
- Detect and investigate those events in Wazuh.
- Configure and use Wazuh's VirusTotal integration.
- Detect a suspicious file through File Integrity Monitoring.
- Investigate the file and its VirusTotal results.
- Perform an appropriate response and remediation action.
- Document the investigation using a SOC analyst reporting approach.

Environment
The project was conducted in an isolated virtual environment using the following tools and technologies:
- Oracle VirtualBox: Hosted and managed the virtual machines.
- pfSense: Configured as the network router and firewall to facilitate communication between virtual machines.
- Wazuh Manager and Dashboard: Collected, monitored, analyzed, and displayed endpoint security events.
- Wazuh Agent: Deployed on the Ubuntu endpoint to collect and forward security events to the manager.
- Ubuntu 26.04.1 LTS: Endpoint operating system used to generate and monitor security events.
- VirusTotal integration: Enriched file alerts with antivirus detection results and threat intelligence.
- EICAR test file: Used to test malware detection in the controlled lab environment.
Alert Detection and Investigation
Three security activities were generated on the Ubuntu endpoint to demonstrate Wazuh's ability to monitor authentication events and privileged command execution.

1. Failed SSH Authentication
Activity: ssh wronguser@localhost
- Timestamp: November 6, 2026, 16:54:13
- Wazuh Rule ID: 5710 — Attempt to login using a non-existent user
- Severity: 5
- Agent: 005 ubuntu-dev
- Source IP: 127.0.0.1
- MITRE ATT&CK IDs: T1110.001, T1021.004
- Tactics: Credential Access and Lateral Movement
Finding: The event recorded an unsuccessful SSH authentication attempt originating from localhost and using a nonexistent username. Wazuh detected the activity and generated an alert under rule 5710. This demonstrates how failed authentication attempts can be surfaced for investigation because repeated or unexpected attempts may indicate password guessing or unauthorized access activity.

2. Successful SSH Authentication
Activity: ssh $USER@localhost 'whoami; hostname; date'
- Timestamp: November 6, 2026, 17:18:42
- Wazuh Rule ID: 5715 — SSH authentication success
- Severity: 3
- Agent: 005 ubuntu-dev
- Source IP: 127.0.0.1
- MITRE ATT&CK IDs: T1078, T1021
Finding: The event recorded a successful SSH authentication using the current Ubuntu user account. The connection originated from localhost, and the command successfully executed whoami, hostname, and date. The event demonstrates Wazuh's ability to monitor successful SSH authentication. In a production environment, an unexpected successful login could warrant further investigation.

3. Successful Sudo Execution
Activity: sudo sh -c 'id; whoami; hostname'
- Timestamp: November 6, 2026, 17:26:10
- Wazuh Rule ID: 5402 — Successful sudo to ROOT executed
- Severity: 3
- Agent: 005 ubuntu-dev
- MITRE ATT&CK ID: T1548.003
- Tactics: Privilege Escalation and Defense Evasion
Finding: The event recorded a successful sudo operation in which the Ubuntu user executed a shell command with root privileges. Wazuh detected this activity using rule 5402. The event alone does not establish malicious activity; it demonstrates Wazuh's ability to monitor privileged command execution.

Malware Detection and Response with VirusTotal
Wazuh Agent Enrollment
The Ubuntu workstation was enrolled as a Wazuh agent and communicated with the Wazuh manager. The dashboard was used to monitor endpoint activity, investigate file integrity alerts, and review VirusTotal threat intelligence results. pfSense provided routing and firewall functionality for the virtual lab.

File Integrity Monitoring Configuration
File Integrity Monitoring (FIM) was configured in Wazuh to monitor the /root directory. This enabled Wazuh to detect file creation, modification, and deletion events within the monitored directory.

Detection and Response
During the malware detection exercise, Wazuh recorded events associated with rule levels 7 and 12. The investigation focused on the VirusTotal detection alert, subsequent file integrity events, and the Active Response execution.
1. VirusTotal Detection Alert
The EICAR test file was introduced into the monitored /root directory. Wazuh detected the file and used the VirusTotal integration to enrich the alert.
- File: /root/eicar.com
- Timestamp: November 20, 2026, 13:27:22
- Wazuh Rule ID: 87105
- Rule Description: VirusTotal alert — 64 engines detected this file
- Severity: 12
- Initial VirusTotal detections: 64
- MITRE ATT&CK ID: T1203
- Tactic: Execution
- Technique: Exploitation for Client Execution
Finding: The alert showed that VirusTotal integration was active and that the file had received 64 positive detections at the time of the initial Wazuh event. The enrichment provided additional context beyond the initial FIM event.

2. File Modification and Deletion
Wazuh recorded a file modification event for /root/eicar.com, followed by a deletion event.
- File: /root/eicar.com
- Deletion Rule ID: 553
- Rule Description: File deleted
- Severity: 7
- MITRE ATT&CK IDs: T1070.004, T1485
- Associated tactics: Defense Evasion and Impact
- Associated techniques: File Deletion and Data Destruction
Finding: The events documented changes to the monitored file, including its modification and subsequent deletion. These records demonstrate how FIM can maintain visibility into file lifecycle events.

3. Active Response Execution
A further Wazuh event confirmed that the configured Active Response mechanism executed the threat removal procedure against /root/eicar.com.
- Wazuh Rule ID: 100092
- Rule Description: Active response remove-threat.sh removed the threat located at /root/eicar.com
- Severity: 12

Threat Intelligence
VirusTotal was used to enrich the Wazuh alerts and provide additional information about the suspicious file.
- File name: eicar.com
- SHA-1 hash: 3395856ce81f2b7382dee72602f798b642f14140
- Initial detection count: 64 antivirus engines
- Subsequent detection count: 65 antivirus security vendors
Further investigation on VirusTotal identified the file as the EICAR test file. Although it was flagged by multiple antivirus vendors, it is a harmless test file intentionally designed to trigger antivirus detection mechanisms. It is commonly used to test security products without introducing an actual malware infection.

The difference between the initial count of 64 and the later count of 65 reflects the different times at which the results were viewed. The later VirusTotal results may have been updated after the initial Wazuh alert was generated.

Results and Findings
The exercise produced the following results:
- Wazuh detected a failed SSH authentication attempt involving a nonexistent username under rule 5710.
- Wazuh recorded a successful SSH authentication under rule 5715.
- Wazuh detected successful sudo execution under rule 5402.
- The Ubuntu agent was enrolled and remained active in the Wazuh dashboard.
- FIM monitored the /root directory and recorded file modification and deletion events.
- VirusTotal enrichment reported 64 detections in the initial Wazuh alert and 65 in the later VirusTotal review.
- Further investigation identified the file as the harmless EICAR test file rather than a genuine malware infection.
- Wazuh Active Response generated an event confirming that the configured removal script removed /root/eicar.com.
The exercise demonstrated the value of correlating endpoint telemetry, file integrity events, and external threat intelligence. It also showed why an analyst should validate the context of a detection before concluding that a real compromise has occurred.
Response and Remediation
After the suspicious file was identified through VirusTotal integration, Wazuh's Active Response mechanism was triggered. The configured response executed the removal procedure against /root/eicar.com.

The response workflow was:
1. Detect the EICAR test file through File Integrity Monitoring.
2. Enrich the alert using VirusTotal.
3. Review the detection results and identify the file as suspicious.
4. Trigger the configured Wazuh Active Response mechanism.
5. Execute the removal procedure against /root/eicar.com.
6. Review the generated alerts and verify the endpoint state.
This automated response was appropriate for the isolated lab because the file had been intentionally introduced for testing and was no longer required after the investigation.
Verification
Following Active Response, the Ubuntu endpoint was checked to confirm that /root/eicar.com was no longer present. The terminal output indicated that the file had been successfully removed.

The Wazuh dashboard was also reviewed to confirm that the Ubuntu agent remained connected to the manager. The displayed endpoint information included:
- Agent ID: 005
- Agent name: ubuntu-dev
- IP address: 192.168.100.223
- Operating system: Ubuntu 26.04.1 LTS
- Status: Active
The alert history was reviewed for related activity during the verification period. These checks confirmed that the file was no longer present in the monitored directory and that endpoint monitoring remained operational.

Conclusion
This project demonstrated a practical SOC workflow using Wazuh SIEM and VirusTotal integration. Wazuh detected and recorded SSH authentication activity and privileged command execution on the Ubuntu endpoint. The exercise also demonstrated how FIM can identify changes to files in a monitored directory and how VirusTotal can enrich an alert with external threat intelligence.

The EICAR test file was detected in /root on November 20, 2026, at 13:27:22. The initial Wazuh alert reported 64 positive detections, while the subsequent VirusTotal review showed 65. Further investigation established that the file was a harmless test file designed to trigger antivirus detection, not evidence of an actual malware infection.

Wazuh's Active Response mechanism executed the configured removal procedure, and subsequent verification confirmed that /root/eicar.com was no longer present and that the Ubuntu agent remained active. Overall, the project provided practical experience in security event monitoring, alert investigation, threat intelligence enrichment, automated response, and post-incident verification.

Author
Gabrielle Lewis
Cybersecurity Student | Aspiring SOC Analyst  

Project: SOC Analysis — Malware Detection, Investigation and Response with Wazuh
Tools: Wazuh, VirusTotal, Ubuntu, pfSense, and VirtualBox
