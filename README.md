# networkwalks-B082-week2-project2-footprinting-and-network_scanning
# PENETRATION TESTING REPORT
### FOOTPRINTING & NETWORK SCANNING PHASES
**W2-PM-FINAL | CYBERSECURITY | NETWORKWALKS**

| Assessment Field | Details |
| :--- | :--- |
| **Pentester Name** | Azaam Hassan |
| **Program/Batch** | B082-Networkwalks |
| **Date** | 21 August 2026 |
| **Modules completed** | W2-PM1 (Multiple Kali Tools)<br>W2-PM5 (Zenmap Scanning) |
| **Client/Target** | 1. Networkwalks (secured written permission already)<br>2. My own local LAN Network |
| **Permission secured from client?** | Yes |
| **Phases covered** | Phase 1: Reconnaissance & Footprinting<br>Phase 2: Scanning & Network Discovery<br>Phase 3-5: In Progress |

---

## 1. Liability Disclaimer
I have performed these activities only on the systems & devices where I had secured written permission or the devices/systems that I own myself. All these materials are for education and research purpose only. Do not use anything from here to break the law. The instructor, the authors and Networkwalks are not responsible for what you do with this knowledge. Every action you take is your own responsibility. Misuse can lead to criminal charges, heavy fines, loss of your job and a permanent record. In most countries unauthorised access is a crime even when nothing is damaged.

---

## 2. Introduction
This report covers footprinting the networkwalks.com domain using multiple Kali Linux tools and scanning my own local network with Zenmap. One module covers the footprinting phase and the other covers the scanning phase, so together they show how an attacker moves from gathering public information to mapping live hosts on a network. It is the Week 2 part of my ongoing internship program at Networkwalks.

All commands were run in Kali Linux (footprinting) and on a Windows PC with Zenmap installed (scanning). Every step below includes the exact command used, the technical results observed, and an analysis of why each finding matters from a security assessment perspective.

---

## 3. Tools Used
The table below lists each tool used in this report and its purpose.

| Tool | Purpose |
| :--- | :--- |
| **Kali Linux & Windows** | Operating systems used for reconnaissance and scanning activities |
| **WHOIS** | Find domain registration details (owner, dates, name servers). |
| **whatweb** | Fingerprint web technologies (server, CMS, plugins, IP). |
| **nslookup** | Resolve the domain name to its IP address using DNS. |
| **curl -I** | Read the HTTP response headers of the website. |
| **wafw00f** | Detect whether a Web Application Firewall protects the site. |
| **dnsrecon** | Enumerate all DNS records (NS, MX, SPF, TXT, SRV). |
| **Zenmap (Nmap GUI)** | Scan the local subnet to find live hosts, IPs and MAC addresses. |
| **Windows CMD** | Local IP and MAC address identification |

---

## 4. Activities Performed

### 4.1 Footprinting & Reconnaissance
I performed reconnaissance against the networkwalks.com domain using six Kali Linux tools: WHOIS, WhatWeb, Nslookup, Curl, Wafw00f and DNSRecon. Each tool was used to collect a different type of information about the target.

* **WHOIS Enumeration:** Used WHOIS to obtain domain registration information and authoritative nameservers. The scan identified registrar GoDaddy.com, LLC (IANA ID 146), creation date 2019-11-06, expiration date 2027-11-06, privacy masking via Domains By Proxy LLC, and delegation to HostGator nameservers (NS6135.HOSTGATOR.COM and NS6136.HOSTGATOR.COM). DNSSEC is confirmed as unsigned.

* **WhatWeb Fingerprinting:** I used WhatWeb to fingerprint technologies on networkwalks.com. The output revealed that the site is running on an Apache HTTP server located at IP 192.232.216.135 (US). It identified WordPress 7.1, Bootstrap 7.1, jQuery 3.7.1, WordPress Download Manager plugin v3.3.58, administrative contact info (info@networkwalks.com), and custom caching headers (x-nginx-cache: WordPress, x-endurance-cache-level: 0).


* **Nslookup DNS Resolution:** Using Nslookup with resolver 8.8.8.8, I resolved networkwalks.com to IPv4 address 192.232.216.135, confirming the hosting address of the primary web application.



* **cURL Header Analysis:** I used Curl with the -I option to inspect HTTP response headers. The output confirmed an HTTP/2 200 OK status, Apache server banner, WordPress caching headers, secure cookie attributes (__wpdm_client), and explicit Link headers revealing the WordPress REST API endpoint (/wp-json/ and /wp-json/wp/v2/pages/53).

 

* **Wafw00f WAF Detection:** I executed Wafw00f to identify perimeter Web Application Firewalls. The scan detected ModSecurity (SpiderLabs) protecting the target web application.



* **DNSRecon Zone Enumeration:** I used DNSRecon to perform comprehensive zone and service record enumeration. The scan identified HostGator authoritative nameservers (50.87.144.87, 192.232.216.131), MX records pointing to mail.networkwalks.com (192.232.216.135), SPF TXT records (v=spf1 +a +mx +ip4:50.87.144.87 +include:websitewelcome.com ~all), 8 cPanel autodiscover SRV records, and exposed the BIND daemon version string ('9.16.23-RH').



---

### 4.2 Network Scanning with Zenmap
For the second activity (Module W2-PM5), I used Zenmap (official GUI front-end for Nmap) on my Windows PC to perform host discovery and topology mapping on my local LAN:
* **Task 1 (Installation):** Downloaded and installed Zenmap with Npcap on Windows.
* **Task 2 (Subnet Identification):** Executed ipconfig in Windows Command Prompt, identifying the local IP address 192.168.xx.1 and LAN subnet mask 255.255.255.0.
* **Task 3 (Live Host Discovery):** Configured Zenmap with target 192.168.56.1/24 and executed a Ping Scan (nmap -sn 192.168.xx.1/24).
* **Task 4 (Host Count):** A total of six (6) hosts were identified as live on the local subnet (including the scanning PC).
* **Task 5 (Live IP Addresses):** The live IP addresses discovered were: 192.168.56.1, 192.168.xxx.xx, 192.168.xxx.xx, 192.168.xxx.xxx, 192.168.xxx.xxx, and 192.168.xxx.xx.
* **Task 6 (MAC Addresses & Vendors):** Submitted on networkwalks.com
* **Task 7 (Topology Generation):** Navigated to the Zenmap Topology tab, turned on the legend, and saved the network topology graph in PDF format.

<p align="center">
</p>

---

## 5. Risk Analysis / Impact
Based on the technical data gathered from the footprinting and network scanning activities, I analyzed the following security observations and potential attack surface risks:

| # | Risk / Finding | Evidence / Observation | Potential Impact | Risk Level |
| :-: | :--- | :--- | :--- | :-: |
| **1** | **DNS Daemon Software Version Disclosure** | DNSRecon disclosed BIND Version '9.16.23-RH' on authoritative nameservers | Allows adversaries to query vulnerability databases for version-specific CVEs affecting BIND 9.16.23-RH, facilitating targeted service disruption or exploit planning. | **● Medium**<br>(CVSS: 5.3) |
| **2** | **CMS & Plugin Version Information Exposed** | WhatWeb identified WordPress 7.1 and WP Download Manager 3.3.58 | Exposing specific CMS and plugin versions allows attackers to identify unpatched vulnerabilities or targeted proof-of-concept exploits without blind fuzzing. | **● Medium**<br>(CVSS: 4.3) |
| **3** | **WordPress REST API Endpoint Exposure** | cURL response headers exposed /wp-json/ and /wp-json/wp/v2/pages/53 | Exposed API endpoints enable unauthenticated attackers to automate user enumeration (/wp-json/wp/v2/users) and map backend resources for brute-force attacks. | **● Low**<br>(CVSS: 3.1)|
| **4** | **Permissive SPF Email Configuration** | DNSRecon identified SPF record configured with soft fail (~all) | Allows unauthorized mail servers to send spoofed emails originating from @networkwalks.com with only a soft failure flag, increasing phishing delivery risks. | **● Low**<br>(CVSS: 3.1) |
| **5** | **DNSSEC Cryptographic Signing Inactive** | WHOIS and DNSRecon confirmed DNSSEC status as 'unsigned' | Leaves DNS responses vulnerable to cache poisoning and spoofing attacks, potentially allowing malicious resolvers to redirect traffic. | **● Low**<br>(CVSS: 2.6)|
| **6** | **Hosting IP & Server Header Disclosures** | Nslookup & cURL revealed IP 192.232.216.135 and Server: Apache | Reveals direct network location and web server software, assisting attackers in infrastructure mapping. | **● Low**<br>(CVSS: 2.5) |
| **7** | **Internal Subnet Host & Hypervisor Visibility** | Zenmap ping sweep mapped 6 active hosts with | Internal network visibility facilitates lateral movement and reconnaissance across virtual workloads if network segmentation is absent. | **● Low**<br>(Internal) |


---

## 6. Recommendations
Based on the observations from these activities, I recommend the following security improvements:
* **Suppress authoritative DNS daemon versions:** Configure authoritative BIND nameservers with 'version none;' inside named.conf to prevent remote version fingerprinting via CHAOS queries.
* **Sanitize web technology & CMS metadata:** Disable WordPress generator meta tags and configure Apache with 'ServerTokens Prod' and 'ServerSignature Off' to conceal web server and plugin details.
* **Restrict WordPress REST API access:** Disable or enforce authentication on sensitive REST API routes (such as /wp-json/wp/v2/users) to mitigate automated user enumeration.
* **Deploy HTTP security hardening headers:** Implement critical security headers including HSTS (Strict-Transport-Security), X-Frame-Options, X-Content-Type-Options, and Content-Security-Policy across the web server.
* **Enforce strict SPF & DMARC policies:** Transition the SPF record policy from soft fail (~all) to hard fail (-all) and publish an active DMARC record to prevent email spoofing.
* **Enable DNSSEC signing:** Configure DNSSEC keys on authoritative nameservers and submit DS records to GoDaddy to provide cryptographic origin authentication.
* **Maintain WAF monitoring & rule updates:** Keep ModSecurity enabled and tuned with updated OWASP Core Rule Sets (CRS) to actively block malicious payloads.
* **Implement internal network segmentation:** Enforce VLAN isolation and private subnet access controls on internal subnets to restrict unauthorized host discovery and lateral movement.

---

## 7. Conclusion
During Week 2 of my Cybersecurity & Ethical Hacking internship, I completed practical activities covering footprinting, reconnaissance and network scanning.

In the footprinting activity (W2-PM1), I used six Kali Linux tools to collect intelligence regarding networkwalks.com. The exercises demonstrated how WHOIS provides domain lifecycle and nameserver data, WhatWeb fingerprints CMS and web components, Nslookup resolves DNS mapping, Curl inspects HTTP headers and REST endpoints, Wafw00f identifies WAF defenses, and DNSRecon uncovers zone records and daemon software versions.

In the network scanning activity (W2-PM5), I used Zenmap on Windows to discover live hosts, identify physical MAC addresses and hypervisor vendors, and generate a visual network topology on my local LAN subnet.

These exercises demonstrated that information gathering is an essential foundational stage in cybersecurity. Security professionals can uncover substantial architectural insights before attempting any active interaction. Documenting technical observations clearly with realistic risk evaluations and remediation steps is critical for effective vulnerability management.

---

## 8. Evidences Collected
The following terminal outputs and scan results were captured during the execution of Module W2-PM1 (Footprinting) and Module W2-PM5 (Zenmap Scanning):

> All Evidence Screenshots are present in the [`evidence_screenshots/`](evidence_screenshots/) folder.

---

<p align="center"><b>- End -</b></p>

Author Info:

Name : Azaam Hassan

Batch : NetworkWalks B082

LinkedIn: https://www.linkedin.com/in/azaam-hassan/
