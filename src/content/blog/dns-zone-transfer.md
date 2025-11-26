---
title: "HTB: DNS Zone Transfer"
description: "Write-up for the HTB DNS Zone Transfer (Information Gathering - Web Edition) room."
pubDate: 2025/11/24
tags: ["HTB","DNS", "Zone Transfer"]
heroImage: '../../assets/blog-placeholder-3.jpg'
draft: false
---

Target IP: 10.129.28.199  
Local IP: 10.10.14.179

**Note: All personal identifiers have been removed from the screenshots. The internal HTB IP ranges (10.10.x.x) and training domains remain unredacted, as they are intentionally exposed within the Hack The Box environment and do not represent real-world infrastructure.**

<br>

<br>

## <u>Objective</u>

Perform a zone transfer on the domain "**inlanefreight.htb**" to enumerate DNS records and identify potentially exposed information about internal systems.

<br>

## <u>CIA Triad Impact:</u>

* **Confidentiality:** High \- Exposing internal infrastructure details, such as subdomains and IPs, poses a serious risk to confidentiality by providing attackers with valuable information for further exploitation.  
* **Integrity:** Low \- No direct impact on the integrity of data or services  
* **Availability:** Low \- No disruption to services.

<br>

## <u>CVSS v3.1 Score:</u>

* **Base Score:** 7.5 (High - AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:N/A:N) \- The vulnerability has a high severity due to its network-based exploitability and high impact on confidentiality (since it leaks internal subdomain information), but it does not affect system integrity or availability.

<img src="/public/web-pentesting-path/dnszonetransfer/cvss-3-1-score.png" alt="Screenshot of DNS Zone Transfer request" width="800"/>

<br>

## <u>Questions</u>

<br>

### <u>Question 1:</u> 
**After performing a zone transfer for the domain inlanefreight.htb on the target system, how many DNS records are retrieved from the target system's name server? Provide your answer as an integer, e.g, 123\.**

#### Method

To perform a DNS zone transfer, I used the dig command to query the target name server directly:
<img src="/public/web-pentesting-path/dnszonetransfer/screenshot1redacted.png" alt="Screenshot of DNS Zone Transfer request" width="800"/>

#### Findings

The zone transfer was successful, returning 22 DNS records. This indicates that the DNS server is misconfigured as it allows unauthorised zone transfers.

#### Answer

22

<br>

### <u>Question 2:</u> 

**Within the zone record transferred above, find the IP address for ftp.admin.inlanefreight.htb. Respond only with the IP address, eg 127.0.0.1**

#### Method

To find the IP address associated with the subdomain "ftp.admin.inlanefreight.htb", I filtered the output from the zone transfer using grep:
<img src="/public/web-pentesting-path/dnszonetransfer/screenshot2redacted.png" alt="Screenshot of zone transfer request using grep" width="800"/>

<br>

#### Findings

The IP address for ftp.admin.inlanefreight.htb is 10.10.34.2

<br>

#### Answer

10.10.34.2

<br>

### <u>Question 3:</u> 

**Within the same zone record, identify the largest IP address allocated within the 10.10.200 IP range. Respond with the full IP address, eg 10.10.200.1**

<br>

#### Method

No commands were required for this step. The IP was manually reviewed from the zone transfer output:
<img src="/public/web-pentesting-path/dnszonetransfer/screenshot3redacted.png" alt="Screenshot of largest IP in DNS Zone Transfer request" width="800"/>

<br>

#### Findings

The largest IP address within the 10.10.200 range was 10.10.200.14

<br>

#### Answer

10.10.200.14

<br>

## <u>Conclusion</u>

The zone transfer vulnerability identified on the inlanefreight.htb domain presents a significant confidentiality risk by exposing sensitive internal network details, such as subdomains, hostnames, IP addresses and infrastructure layout. The integrity and availability of the DNS service is unaffected, but the exposed data could be used by attackers for targeted enumeration or lateral movement.

To mitigate the risk, it is recommended to restrict AXRF to authorised name servers only. This measure should help reduce the potential for targeted attacks.

