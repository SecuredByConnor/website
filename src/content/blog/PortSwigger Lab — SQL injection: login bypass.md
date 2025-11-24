---
title: "PortSwigger Lab — SQL injection: login bypass"
description: "Write-up for the PortSwigger Web Security Academy lab demonstrating an SQL injection login bypass, with PoC and remediation."
pubDate: 2025/11/06
tags: ["PortSwigger","SQLi","Authentication","Burp Suite"]
heroImage: '../../assets/blog-placeholder-3.jpg'
draft: false
---
## **TL;DR**
I bypassed authentication via SQL injection by using the SQL comment sequence "--" which removed the password check from the "WHERE" clause of the query. I used the username "administrator'--" and used a blank password " '' "

This sucessfully logged me in as the administrator.


## **Lab reference**

* https://portswigger.net/web-security/learning-paths/sql-injection/sql-injection-subverting-application-logic/sql-injection/subverting-application-logic 


## **Scope**

1. **Target URL:** https://0a4000...00c4.web-security-academy.net/login 
2. **Authorized tester:** Portswigger lab assinged to me
3. **Provided credentials:** Administrator (username); password unknown
4. **Allowed actions:** 
    * Interact with the login form 
    * Submit crafted inputs 
    * Intercept and replay requests
    * Examine application responces for proof of authentication.
5. **Out of scope:** 
    * Attakcing infrastrucutre outside the given URL
    * Privildge escalation beyond the lab
    * Data exfiltration of real user data
    * Any attack on third-party systems
6. **Saftey and sanitisation:** 
    * Do not publish raw credentials or session tokens
    * Redact or replace sensitive tokens with placeholders before posting.






## **Goals and learning objectives**

* **Primary Goal:** Bypass login to authenticate as administator without knowing the password.

* **Skills practiced:** 
    * Manual SQL injection 
    * Intercepting requests with burp 
    * Crafting payloads
    * Safe testing practices


## **Lab environment**

1. **Target URL:**  https://0a4000...00c4.web-security-academy.net/login 
2. **Tools Used:**
    * Burp Suite
    * Firefox with proxy
    * MySQL knowledge



## **Reconnaissance**

* **What I observed on the page:**

## **Methodology**

*  



## **Step 1 — Capture the login request**

* 

## **Step 2 — Basic injection tests**

* 

## **Step 3 — Confirming the vulnerability**

* 

## **Step 4 — Exploit and proof**

* 

## **Payloads and explanation**

* Payload: 

* Why it works: 


## **Root cause (technical explanation)**

* 

* 

## **Mitigation and remediation**

* Short fixes: 

* Additional recommendations:


## **Lessons learned**

* 

* 



