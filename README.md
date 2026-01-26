# Soc-Home-Virtualbox
SOC home lab using VirtualBox  to analyze Windows event logging, ICMP traffic, firewall behavior, and authentication events.
# SOC Home Lab – VirtualBox, Windows Event Logging, and ICMP Analysis

## Environment
- Host OS: Windows 11
- Hypervisor: Oracle VirtualBox
- Guest OS:
  - Kali Linux 2025.2
  - Windows 11
- Network Mode: Host-only Adapter

## Lab Setup and Troubleshooting
VirtualBox was installed to create an isolated SOC lab environment consisting of Kali Linux and Windows 11 virtual machines. During initial startup, the Kali Linux VM failed to launch due to a VirtualBox kernel driver error (`VERR_LDR_IMPORTED_SYMBOL_NOT_FOUND`), commonly associated with driver or virtualization conflicts.

After reinstalling VirtualBox and adjusting system virtualization settings, both virtual machines successfully booted and were prepared for security testing.

---

## Step 1: Network Configuration and Connectivity Verification

### What I Did
Both virtual machines were configured to use a host-only network adapter. IPv4 addresses were identified using `ip a` on Kali Linux and `ipconfig` on Windows. ICMP echo requests were sent between systems to verify internal network connectivity.

### What I Learned
This confirmed both systems were correctly assigned IP addresses within the same subnet and could communicate at Layer 3. Validating connectivity early prevents misattributing future security test results to basic network issues.

### Purpose
To verify network segmentation and internal communication before generating security-relevant traffic, mirroring standard SOC validation procedures.

---

## Step 2: Windows Event Logging Exploration

### What I Did
Windows Event Viewer was examined to understand how the operating system records system and application activity. System and Application logs were reviewed to observe service behavior and system state changes. Security log access was restricted due to limited administrative privileges.

### What I Learned
Windows organizes telemetry across multiple log categories, and meaningful analysis can still be performed even with restricted access. Properly documenting access limitations is critical in real-world SOC environments.

### Purpose
To simulate real-world SOC conditions where analysts may not have full administrative privileges but must maintain situational awareness using available telemetry.

---

## Step 3: ICMP Traffic and Firewall Behavior

### What I Did
ICMP traffic was generated from Kali Linux to the Windows 11 system within the host-only network. Windows did not respond to ICMP echo requests due to firewall rules blocking inbound ICMP traffic.

### What I Learned
ICMP failure does not inherently indicate a network outage and often reflects enforced security controls. Additionally, basic network traffic is not logged by default in Windows Security logs, requiring analysts to correlate network behavior with host policies.

### Purpose
To reinforce that security controls can intentionally limit reachability and visibility, and analysts must distinguish normal enforcement from true security incidents.

---

## Step 4: Authentication Event Analysis (Windows Security Logs)

### What I Did
Multiple failed authentication attempts were generated on the Windows system using incorrect credentials, followed by a successful login. Windows Security logs were reviewed for authentication-related events.

### What I Learned
Windows records failed logons (Event ID 4625) and successful logons (Event ID 4624). A sequence of repeated failures followed by success may represent user error or potential credential compromise.

### Purpose
To practice correlating authentication events over time from a SOC analyst perspective and assess whether activity represents benign behavior or requires further investigation.
