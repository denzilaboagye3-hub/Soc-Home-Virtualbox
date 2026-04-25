VirtualBox Documentation Soc lab
SOC Home Lab - VirtualBox, Windows Event Logging, and ICMP Analysis
##Environment
	Host OS: Windows 11
	Hypervisor: Oracle VirtualBox
	Guest OS:
	Kali Linux 2025.2
	Windows 11
	Network Mode: Host-only Adapter
	Setup: 
VirtualBox was installed to create an isolated SOC lab environment consisting of Kali Linux and Windows 11 virtual machines. During initial startup, the Kali Linux VM failed to launch due to a VirtualBox kernel driver error (`VERR_LDR_IMPORTED_SYMBOL_NOT_FOUND`), commonly associated with driver or virtualization conflicts on Windows hosts.

After reinstalling VirtualBox and adjusting system virtualization settings, both virtual machines successfully booted and were prepared for network and security testing.
Result Code:	E_FAIL (0x80004005)
Component:	ConsoleWrap
Interface:	IConsole {6ac83d89-6ee7-4e33-8ae6-b257b2e81be8}


Step 1:
What I Did: 
I configured both Kali Linux and Windows 11 virtual machines to use a host-only network adapter in VirtualBox. I identified each system's IPv4 address using IP a on Kali Linux and ipconfig on Windows. ICMP echo requests were then sent between the systems to verify internal network connectivity. 

What I learned:
This step confirmed that both virtual machines were correctly assigned IP addresses within the same subnet and could communicate at Layer 3. Verifying connectivity early ensures that later security testing results are not caused by basic networking misconfigurations.

Purpose:
The purpose of this step was to validate network segmentation and internal communication before generating security-relevant activity, mirroring standard SOC validation procedures.
 
Kali Linux IP configuration (ip a):
Confirms the Kali Linux system was assigned an IPv4 address on the host-only network interface
 
VirtualBox host-only network configuration:
Shows the virtual network adapter configuration used to isolate internal VM communication.
 
Windows 11 IP configuration (ipconfig):
Confirms the Windows system received an IPv4 address within the same subnet as the Kali Linux VM
 

ICMP connectivity verification:
Successful ICMP echo replies confirm Layer 3 connectivity between the Windows and Kali virtual machines

Step 2:
What I Did:
I examined the Windows event viewer to understand how the operating system records system and application activity. I reviewed the system and application logs to observe service behavior, system state changes, and application events. Access to the security log required administrative privileges, which were restricted in this environment.

What I Learned:
This step demonstrated how Windows organizes event data across multiple log categories and how analysts can still extract meaningful telemetry even with limited access. It reinforced the importance of documenting access constraints during the counting analysis.

Purpose:
The purpose of this step was to simulate real- world SOC conditions where analysts may not have full administrative privileges and must rely on available logs to maintain situational awareness.
 
System log event inspection:
Demonstrates visibility into system-level activity such as service state changes.
 
Application log analysis:
Shows application-level events that provide context during system monitoring and investigations.
 
Expanded Windows Logs view:
Shows are available in log categories used during host-based monitoring.
 
Windows Event Viewer overview:
Displays the centralized logging interface used to analyze system, application, and security events.

Step 3:
What I Did: 
In step 3, controlled ICMP traffic was generated from a Kali Linux system to a Windows 11 host within a host-only virtual network. This simulated basic internal network communication in a controlled lab environment. The Windows host did not respond to ICMP echo requests due to a firewall policy, resulting in Windows firewall rules blocking ICMP echo requests. This showed me that layer 3 connectivity between hosts and validated that the host-only network was functioning as intended before generating security-relevant traffic

What I learned:
This step demonstrated that packet loss or an ICMP echo request being blocked does not necessarily indicate a connectivity issue but often reflects enforced firewall rules. It also showed that basic network traffic is not logged by default in Windows Security logs and that analysts must understand both firewall behavior and log visibility when interpreting potential security events.

The purpose:
The purpose of this topic was to reinforce that security controls can intentionally limit visibility and reachability. SOC analysts must differentiate between normal security enforcement and actual network failures or attacks by correlating traffic behavior with system logs and policy context.
 
ICMP request:
Initial network reachability test initiated from Windows CLI
 
Kali ping Windows unreachable:
ICMP traffic is blocked by the firewall policy.

Step 4:

What I Did:
I generated multiple failed authentication attempts within a short timeframe by entering incorrect credentials repeatedly on the Windows 11 login screen. After several failed attempts, a successful login was performed using the correct password. The Windows Security log was then filtered to isolate authentication-related events (Event IDs 4625 and 4624), and multiple failed login events were identified occurring within seconds of each other.
What I learned:
This step reinforced that authentication logs must be analyzed in context rather than individually. A single failed login is typically benign, but multiple failed attempts within a short timeframe create a pattern that may indicate suspicious behavior such as brute-force attempts. I also learned that timing and frequency of events are critical factors in determining whether activity is normal or potentially malicious.
The purpose:
The purpose of this step was to simulate real-world authentication monitoring by generating and analyzing login failures. This reflects how SOC analysts identify abnormal login patterns and distinguish between user error and potential unauthorized access attempts.

 
Failed logon event (Event ID 4625):
Records multiple failed authentication attempts caused by incorrect credentials. This event captures login failure details relevant to brute-force or credential misuse analysis. 

 
Successful logon event (Event ID 4624):
Shows successful authentication following prior failed attempts, demonstrating how correlated log events can indicate either user error or potentially suspicious access patterns.


Reviewing both failed (4625) and successful (4624) logon events reinforces the importance of correlating authentication activity over time. A sequence of repeated failures followed by success may indicate benign behavior, such as mistyped passwords, or warrant further investigation for potential credential compromise.

Step 5:

What I did:
I analyzed the sequence and timing of authentication events in the Windows Security log to identify patterns in failed login attempts. Multiple Event ID 4625 entries were observed within a short time window (approximately a few seconds apart), followed by a successful Event ID 4624 login. I reviewed the timestamps and grouping of these events to determine whether the behavior resembled normal user error or potential malicious activity.
What I Learned:
This step demonstrated that security analysis is based on identifying patterns rather than isolated events. Repeated failed login attempts occurring rapidly can indicate potential brute-force or credential-guessing activity, especially if targeting a specific account. However, similar patterns can also result from user mistakes, highlighting the importance of context, frequency, and follow-up activity when evaluating security events.

Purpose:
The purpose of this step was to practice correlating multiple log events over time to simulate real SOC analysis workflows. Instead of viewing logs individually, this approach focuses on building a timeline of activity to determine whether behavior is normal or requires escalation.
   

Final Lab conclusion
This lab demonstrated the ability to configure an isolated virtual SOC environment, validate internal network communication, and analyze host-based security logs. Authentication events were successfully generated and correlated, showing how repeated failed login attempts followed by a successful login can represent either benign user behavior or a potential security concern. This exercise reinforced the importance of log correlation, network awareness, and understanding system-level security controls when performing incident analysis.
