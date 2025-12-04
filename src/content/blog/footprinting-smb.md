---
title: "HTB: Footprinting SMB"
description: "Write-up for the HTB Footprinting SMB room in the penetration testing path"
pubDate: 2025/12/04
tags: ["HTB","SMB", "Penetration Tester Path"]
heroImage: '../../assets/blog-placeholder-3.jpg'
draft: false
---

**HTB Path:** Penetration Testing
<br>
**Module:** Footprinting
<br>
**Section:** Host Based Enumeration
<br>
<br>


**Note: Note: All personal identifiers have been removed from the screenshots. The internal HTB IP ranges (10.10.x.x) and training domains remain unredacted, as they are intentionally exposed within the Hack The Box environment and do not represent real‑world infrastructure. Additionally, the IP addresses shown may vary throughout this write‑up due to the dynamic nature of HTB machines, which frequently change their IP addresses as part of their timed and resource‑allocation mechanisms.**



<br>

<br>

## <u>Objective</u>

To perform footprinting and emuneration of the Server Message Block (SMB) service running on the target system using various tools such as nmap, rpclient, samdump.py, smbmap, crackmapexec, and enum4linux-ng to gather information about the service version, available shares, domain, and user accounts.

<br>

## <u>CIA Triad Impact:</u>

* **Confidentiality:** High \-  Anonymous access to the SMB service allows enumeration of shared resources, user accounts, and domain information (OS version, domain name). This poses a serious risk to confidentiality as it provides an attacker with valuable information for further exploitation.
* **Integrity:** Low  \- The accessible share is read-only. An attacker cannot modify, upload, or delete any files on the system, which severely limits the impact on data integrity.
* **Availability:** Low  \- This vulnerability does not disrupt system uptime or stop legitimate users from accessing the application. There is no disruption in availability.

<br>

## <u>CVSS v3.1 Score:</u>

* **Base Score:** 7.5 (High) (AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:N/A:N)  \- The vulnerability has a high severity due to its network-based exploitability and high impact on confidentiality, but does not affect integrity because files are read-only, and has no effect on availability.

<img src="/pentesting-path/footprinting/smb/cvss-score-1.png" alt="" width="800"/>
<br>
<img src="/pentesting-path/footprinting/smb/cvss-score-2.png" alt="" width="400"/>

<br>

--- 
<br>

## <u>Questions</u>

<br>

### <u>Question 1:</u> 
**What version of the SMB server is running on the target system? Submit the entire banner as the answer.**

#### <u>Method</u>
To find the version of the SMB server running, nmap was used to enumerate the service version of the SMB service:
<br>
**Command:** nmap - sV -p 445 10.129.202.5
<br>
**Arguments:**
<br>
-sV: Detects serivce version
<br> 
<img src="/pentesting-path/footprinting/smb/question-1/nmap-sv-option.png" alt="" width="800"/>
<br>
-p: Specifies port number
<br>
<img src="/pentesting-path/footprinting/smb/question-1/nmap-p-option.png" alt="" width="800"/>
<br>
**<u>Result</u>**
<br>
<img src="/pentesting-path/footprinting/smb/question-1/nmap-service-version-scan.png" alt="" width="800"/>
<br>

#### <u>Findings</u>
The nmap scan was successful, the SMB server is running on port 445 and smbd 4.6.2.

#### <u>Answer</u>
Samba smbd 4.6.2
<br>
<br>

---
### <u>Question 2:</u> 

**What is the name of the accessible share on the target?**

#### <u>Method</u>
To find the name of the accessible share, smbmap was used to enumerate shares:
<br>
**Command:** smbmap -H 10.129.202.5
<br>
**Arguments:** 
<br>
-H: Specifies host
<br>
<img src="/pentesting-path/footprinting/smb/question-2/smbmap-h-option.png" alt="" width="800"/>

#### <u>Findings</u>
Three shares are discovered, but only sambashare is accessible with read-only permissions. This indicates that although the confidentiality threat is high, the integrity threat remains low because files with read-only access cannot be manipulated. Availability also remains low.

#### <u>Answer</u>
sambashare
<br>
<br>

---
### <u>Question 3:</u> 

**Connect to the discovered share and find the flag.txt file. Submit the contents as the answer.**

<br>

#### <u>Method</u>
To retrieve the flag from the SMB share, smbclient was used to connect and navigate target machine:
<br>
<br>
**1. Connect to target machine**
<br>
**Command:** smbclient //10.129.219.56/sambashare
<br>
<img src="\pentesting-path\footprinting\smb\question-3/smbclient-connecting-command.png" alt="" width="800"/>

**2. View inside current target directory**
<br>
**Command:** ls
<br>
<img src="\pentesting-path\footprinting\smb\question-3/initial-smb-ls-command.png" alt="" width="800"/>

**3. Go inside contents directory, and view its contents**
<br>
**Command:** cd contents
<br>
**Command:** ls
<br>
<img src="\pentesting-path\footprinting\smb\question-3/inside-contents-directory.png" alt="" width="800"/>

**4. Download the flag.txt file**
<br>
**Command:** get flag.txt
<br>
<img src="\pentesting-path\footprinting\smb\question-3/download-flag-txt.png" alt="" width="800"/>

**5. View local folder/flag contents flag while in smbclient session**
<br>
**Command:** !ls
<br>
**Command:** !cat flag.txt
<br>
**Arguments:**
<br> 
!: Allows to view file and folders on local machine while in smbclient.
<br>
<img src="\pentesting-path\footprinting\smb\question-3/view-local-contents-smbsession.png" alt="" width="800"/>

#### <u>Findings</u>
The flag.txt file was successfully obtained from the SMB share, and its content were extracted. This confirms the SMB share exposes internal data and enables unauthorised users to access and download sensitive information, making the service vulnerable to enumeration and data leakage attacks.

#### <u>Answer</u>
HTB{o873nz4xdo873n4zo873zn4fksuhldsf}
<br>
<br>

---
### <u>Question 4:</u> 
**Find out which domain the server belongs to.**

#### <u>Method</u>

To find out which domain the server belongs to, rpcclient and Enum4Linux-ng were used:
<br>
<br>

> #### <u>**Rpcclient**</u>
**1. Connect to target using rpcclient**
<br>
**Command:** rpcclient -U "" 10.129.219.56
<br>
**Arguments:**
<br>
-U: Sets the network username
<br>
"": Sets the username as blank
<img src="\pentesting-path\footprinting\smb\question-4/rpcclient-u-option.png" alt="" width="800"/>
<br>
<img src="\pentesting-path\footprinting\smb\question-4/rpcclient-initial-connect.png" alt="" width="800"/>

**2. Enumerate domain, server and user information for all domains**
<br>
**Command:** rpcclient $> enumdomains
<br>
**Arguments:** N/A
<br>
<img src="\pentesting-path\footprinting\smb\question-4/rpcclient-enumdomains.png" alt="" width="800"/>
<br>
<br>

> #### <u>**Enum4Linux-ng**</u>
**1. Enumerate the target using enum4linux-ng**
<br>
**Command:** enum4linux-ng 10.129.219.56 -A
<br>
**Arguments:** -A combines several different arguments, including:
<br>
**-U:** Gets users via RPC
<br>
<img src="\pentesting-path\footprinting\smb\question-4/u-option-enum4linux.png" alt="" width="800"/>
<br>
**-G:** Gets groups via RPC
<br>
<img src="\pentesting-path\footprinting\smb\question-4/g-option-enum4linux.png" alt="" width="800"/>
<br>
**-S:** Gets shares via RPC
<br>
<img src="\pentesting-path\footprinting\smb\question-4/s-option-enum4linux.png" alt="" width="800"/>
<br>
**-P:** Gets password policy information via RPC
<br>
<img src="\pentesting-path\footprinting\smb\question-4/p-option-enum4linux.png" alt="" width="800"/>
<br>
**-O:** Get OS information via RPC
<br>
<img src="\pentesting-path\footprinting\smb\question-4/o-option-enum4linux.png" alt="" width="800"/>
<br>
**-N:** Does a NetBIOS names lookup
<br>
<img src="\pentesting-path\footprinting\smb\question-4/n-option-enum4linux.png" alt="" width="800"/>
<br>
**-I:** Gets printer information via RPC
<br>
<img src="\pentesting-path\footprinting\smb\question-4/i-option-enum4linux.png" alt="" width="800"/>
<br>
**-L:** Gets additional domain info via LDAP/LDAPS (Only for Domain Controllers)
<br>
<img src="\pentesting-path\footprinting\smb\question-4/l-option-enum4linux.png" alt="" width="800"/>
<br>
<br>
<u>**Result:**</u>
<br>
<img src="\pentesting-path\footprinting\smb\question-4/result-1-enum4linux.png" alt="" width="800"/>
<br>
<img src="\pentesting-path\footprinting\smb\question-4/result-2-enum4linux.png" alt="" width="800"/>
<br>


#### <u>Findings</u>
Running enumdomins through rpcclient revealed the list of domains on the target system, identifying the primary domain associated with the server. A subsequent enumeration using Enum4Linux-ng with the -A option produced consistent results, confirming the same domain information through multiple RPC and NetBIOS‑based queries. This vulnerability poses a significant confidentiality risk by enabling attackers to access sensitive data that should not be publicly available. Integrity and availability are unaffected, but attackers could use the exposed data for targeted enumeration or lateral movement.
<br>
#### <u>Answer</u>
DEVOPS
<br>
<br>

---
### <u>Question 5:</u> 

**Find additional information about the specific share we found previously and submit the customized version of that specific share as the answer.**

#### <u>Method</u>
To find additional information about the share, smbmap was used to enumerate shares:
<br>
**Command:** smbmap -H 10.129.29.184
<br>
**Arguments:**
<br> 
-H: Specifies host
<br>
<u>**Result:**</u>
<br>
<img src="\pentesting-path\footprinting\smb\question-5/smbshare-more-info.png" alt="" width="800"/>
<br>
#### <u>Findings</u>
The enumeration of the SMB share revealed additional metadata, including the share version “InFreight SMB v3.1.” While this information does not directly compromise system integrity or availability, disclosing detailed version identifiers increases the risk to confidentiality. Attackers can use this information to map the environment and identify potential vulnerabilities associated with that specific implementation. To mitigate this risk, it is advisable to restrict anonymous access to SMB enumeration and limit share visibility to authenticated users. This would reduce the likelihood of this information being exploited for targeted attacks.
<br>
#### <u>Answer</u>
InFreight SMB v3.1
<br>
<br>
### <u>Question 6:</u> 

**What is the full system path of that specific share? (format: "/directory/names")**

#### <u>Method</u>
To find the full system path, rpcclient was used:
<br>
**Command:** rpcclient $> netsharegetinfo sambashare
<br>
**Arguments:** N/A
<br>
<u>**Result:**</u>
<br>
<img src="\pentesting-path\footprinting\smb\question-6/rpcclient-full-system-path.png" alt="" width="800"/>
<br>
#### <u>Findings</u>
The full system path of the share was successfully retrieved using the netsharegetinfo RPC call, revealing that the underlying directory is /home/sambauser. Although the share is read-only, disclosing the internal filesystem structure provides attackers with valuable contextual information about user directories and the server layout. This information can aid in more targeted enumeration or privilege escalation attempts. To mitigate this risk, it is important to limit unauthenticated RPC access and strengthen share permissions. Doing so can help prevent the disclosure of internal directory paths and reduce the overall reconnaissance surface.
<br>
#### <u>Answer</u>
/home/sambauser
<br>
**note: The expected answer was in Linux format ("/home/sambauser"), but the result returned was in Windows format ("C:\home\sambauser"), likely due to the underlying system environment or tool defaulting to Windows path notation.**
<br>
<br>

---
 ## <u>Conclusion</u>
The process of footprinting and enumerating the SMB service on the target system successfully revealed critical information, such as the SMB version, accessible shares, and domain affiliation. The tools smbmap and rpcclient were instrumental in identifying an accessible share (sambashare), extracting sensitive information from the server, and confirming the server’s domain association (DEVOPS).

While the integrity of the server’s files remained intact with read-only access and availability was not affected, the disclosed metadata posed a significant risk to confidentiality. Unauthorized access to domain and share information can facilitate targeted attacks, allowing attackers to gather intelligence for further exploitation.

To mitigate these risks, it is recommended to restrict access to SMB shares, enforce strong access controls, and ensure that services like RPC are configured to prevent unauthorized enumeration. By implementing these safeguards, the exposure of sensitive data can be minimized, reducing the likelihood of further compromise.


