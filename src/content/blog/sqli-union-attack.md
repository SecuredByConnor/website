---
title: "Web Security Academy: SQLI Union Attack"
description: "Write-up for the SQLI Union Attack Lab"
pubDate: 2025/11/26
tags: ["HTB","SMB", "Penetration Tester Path"]
heroImage: '../../assets/blog-placeholder-4.jpg'
draft: false
--- 

<br>

<br>

## <u>Objective</u>
To determine the number of columns that are being returned by the query by performing a SQL injection UNION attack.


<br>

## <u>CIA Triad Impact:</u>

* **Confidentiality:** High \- The vulnerability allows an attacker to extract data from the database using UNION-based SQL Injection, exposing sensitive information that should be inaccessible. This poses a significant confidentiality risk.
* **Integrity:** Low \- The vulnerability allows for data retrieval but does not change the data. The integrity of the stored data is not affected.
* **Availability:**   \- This vulnerability does not disrupt system uptime or stop legitimate users from accessing the application. There is no disruption in availability.

<br>

## <u>CVSS v3.1 Score:</u>

* **Base Score:** 8.2 (AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:L/A:N)  \- The vulnerability has a high severity due to its potential for remote exploitation and the significant risk it poses to confidentiality. It enables attackers to extract sensitive data from the database. However, the vulnerability does not compromise system integrity or availability, as the focus of the attack is solely on data disclosure.

<img src="/web-security-academy/cvss-3.1-score.png" alt="" width="800"/>

<br>
<br>

## <u>Questions</u>

<br>

### <u>Question 1:</u> 
**To solve the lab, determine the number of columns returned by the query by performing a SQL injection UNION attack that returns an additional row containing null values.**

#### Method
To determine the number of columns required for a UNION-based SQL injection, I used Burp Suite to intercept the request that sets the project category filter. After capturing the request, I sent it to Repeater to allow repeated testing and modification of the payload:

1. Modified the category parameter to "Gifts '+UNION+SELECT+NULL--", which caused the application to return an error.
<img src="/web-security-academy/screenshot1.png" alt="" width="800"/>

2. Added an additional null value to the payload "'+UNION+SELECT+NULL,NULL--"
<img src="/web-security-academy/screenshot2.png" alt="" width="800"/>

3. Continued incrementally adding null values and resending the request until the server responded without an error and "Lab Solved".
<img src="/web-security-academy/screenshot3.png" alt="" width="800"/> <img src="/web-security-academy/screenshot4.png" alt="" width="800"/>

#### Findings
By gradually increasing the number of null values in the UNION query, I identified the exact number of columns the application expects in its SQL queries (3). This confirmed that the application is vulnerable to UNION-based SQL injection, allowing an attacker to manipulate database queries and retrieve unintended data.


#### Answer
3


<br>

## <u>Conclusion</u>
The SQL injection vulnerability found in the product category filter poses a significant risk to confidentiality by allowing unauthorized database queries through UNION-based injection. This vulnerability enables attackers to access the database structure and retrieve sensitive information that should not be publicly available. Although the application's integrity and availability are not affected in this case, the ability to manipulate SQL queries is a serious flaw that could be exploited further.

To mitigate this risk, it is recommended to implement parameterized queries or prepared statements, validate and sanitize user input, and ensure that database error messages are suppressed or handled securely. These measures will considerably reduce the likelihood of SQL injection attacks and enhance the application's overall security posture.


