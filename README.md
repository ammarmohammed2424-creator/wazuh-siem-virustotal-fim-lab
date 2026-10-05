# wazuh-siem-virustotal-fim-lab
Hands-on SOC lab demonstrating automated file analysis by integrating Wazuh SIEM with VirusTotal API. Features agent-based FIM scanning on Windows endpoints and high-severity threat alert verification on the Wazuh Dashboard.


## Detailed Project Workflow & Implementation

### 1. Threat Intelligence Credential Provisioning
Provisioned an active API key from VirusTotal's threat intelligence platform[cite: 1]. This key allows the local Wazuh SIEM deployment to programmatically query VirusTotal's multi-engine database using file hashes generated on protected endpoints, eliminating manual hash lookups during incident triage[cite: 1, 3, 4].

<img width="1885" height="802" alt="Screenshot 2026-10-05 122949" src="https://github.com/user-attachments/assets/41212400-64cd-4769-aa4c-8f88ffd5b745" />



---

### 2. Management Session Establishment
Established an SSH terminal session from the local management host to the Ubuntu Wazuh Manager Virtual Machine (IP). Running administrative commands over SSH enables secure, out-of-band server administration and configuration management without requiring a local VM GUI session.

<img width="1178" height="708" alt="Screenshot 2026-10-05 123635" src="https://github.com/user-attachments/assets/41cbaab2-ba80-4d55-8d5f-7da6216f652e" />


---

### 3. VirusTotal API Integration Configuration
Configured the primary server configuration file (`/var/ossec/etc/ossec.conf`) on the Wazuh Manager using Nano[cite: 3]. Embedded a custom `<integration>` block specifying the `virustotal` module, target API credentials, and rule triggers (`syscheck`) to instruct the manager to automatically evaluate file modification events against global threat databases[cite: 3].

<img width="1474" height="705" alt="Screenshot 2026-10-05 124044" src="https://github.com/user-attachments/assets/6109c985-389c-408d-baf2-e481ac4531de" />

---

### 4. On-Demand Endpoint Scan Trigger
Utilized Wazuh's command-line binary `agent_control` from the Manager terminal to execute an on-demand Syscheck scan against active Windows endpoint `002` (`AGENT NAME`)[cite: 5]. This forced the endpoint's File Integrity Monitoring (FIM) engine to scan monitored directories, compute updated cryptographic hashes (MD5/SHA256) for local files, and pass them back to the server[cite: 5].

<img width="1912" height="939" alt="Screenshot 2026-10-05 135223" src="https://github.com/user-attachments/assets/dbb08c1f-584b-4683-a21a-bc387e945199" />


---

### 5. Endpoint Scan Verification
Polled the endpoint's execution status using the `/var/ossec/bin/agent_control -i 002` utility on the Manager CLI[cite: 5, 6]. Confirmed that the File Integrity Monitoring scan successfully completed (`Syscheck last ended at: Mon Oct 5 09:47:48 2026`), ensuring all newly added files had their hashes submitted for API inspection[cite: 6].


<img width="1908" height="881" alt="Screenshot 2026-10-05 135233" src="https://github.com/user-attachments/assets/752aad66-5874-4b8a-88af-2b28bae727a2" />

---

### 6. Security Event Detection & Alert Triage
Navigated to the Wazuh Security Events dashboard to analyze ingested telemetry[cite: 4]. Verified that the FIM change event successfully triggered the VirusTotal integration, generating a high-severity **Level 12 security alert** (Rule ID `87105`) after VirusTotal identified 65 security engines flagging the endpoint test file as malicious[cite: 4].

<img width="1912" height="797" alt="Screenshot 2026-10-05 135121" src="https://github.com/user-attachments/assets/80841d31-4c3f-44e8-9bf8-fcca3dcfdc14" />
