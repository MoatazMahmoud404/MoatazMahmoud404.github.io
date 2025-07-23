---
title: "Username Enumeration via Response Timing (PortSwigger Lab Walkthrough)"
date: 2025-07-23
categories: [Web Security, PortSwigger]
tags: [username enumeration, brute force, timing attack, burp suite, X-Forwarded-For]
author: 0xReDrag0n
author_bio: "Offensive Security Enthusiast & Blogger"
image:
  path: v0/b/xredrag0n.appspot.com/o/Attachment-Posts%2FPost-2025-07-23-Username-enumeration-via-response-timing%2Fbanner.jpg?alt=media&token=25f2f340-e7ef-4ebc-8000-466e2d022ded
seo:
  title: "Username Enumeration via Response Timing | PortSwigger Lab Walkthrough"
  description: "Step-by-step guide to exploiting username enumeration via response timing and bypassing IP-based brute-force protection in PortSwigger's Web Security Academy."
  keywords: [username enumeration, timing attack, brute force, X-Forwarded-For, PortSwigger, web security]
published: true
---

{% include pageviews.html %}

# Username Enumeration via Response Timing (PortSwigger Lab)

**Lab:** [Username enumeration via response timing](https://portswigger.net/web-security/authentication/password-based/lab-username-enumeration-via-response-timing)

This walkthrough demonstrates how to exploit username enumeration vulnerabilities using response timing, as featured in PortSwigger's Web Security Academy. The lab also introduces IP-based brute-force protection, which we will bypass using HTTP header manipulation.

**Difficulty:** Practitioner

---

## Lab Description

This lab is vulnerable to username enumeration through response time analysis. The objective is to identify a valid username, brute-force the corresponding password, and successfully log in to access the account page.

**Credentials Provided:**
- Username: `wiener`
- Password: `peter`

**Resources:**
- [Candidate usernames](https://portswigger.net/web-security/authentication/auth-lab-usernames)
- [Candidate passwords](https://portswigger.net/web-security/authentication/auth-lab-passwords)

**Key Points:**
- The login mechanism is susceptible to brute-force attacks.
- Timing differences in responses can reveal valid usernames.
- The application implements IP-based brute-force protection.
- Lists of possible usernames and passwords are provided.

---

## Objectives
- Enumerate a valid username.
- Brute-force the corresponding password.
- Log in and access the account page.

---

## Step 1: Understanding the Brute-Force Protection

At first glance, it seems straightforward to brute-force the login and retrieve valid credentials using a Pitchfork attack in Burp Suite. However, after several failed attempts, the application blocks the IP address, indicating the presence of IP-based brute-force protection.

![Screenshot showing IP block after multiple failed login attempts](https://firebasestorage.googleapis.com/v0/b/xredrag0n.appspot.com/o/Attachment-Posts%2FPost-2025-07-23-Username-enumeration-via-response-timing%2Fscreen-01.png?alt=media&token=64ad3cc6-6ad1-480e-bc4f-60c3b324d415)
*Figure 1: IP address blocked after repeated failed login attempts*

To bypass this restriction, we can manipulate HTTP request headers. Specifically, the `X-Forwarded-For` header is supported, allowing us to spoof our IP address and circumvent the brute-force protection.

> **Tip:** Use a random value for the `X-Forwarded-For` header in each request to bypass IP-based brute-force protection.
{: .prompt-tip }

---

## Step 2: Username Enumeration via Response Timing

To enumerate valid usernames, set the first position in Burp Suite Intruder for the `X-Forwarded-For` header (using a random value for each request) and the second position for usernames from the provided list (including `wiener`).

> **Tip:** Use an intentionally long password to maximize timing differences.
{: .prompt-tip }

![Burp Suite Intruder setup for username enumeration](https://firebasestorage.googleapis.com/v0/b/xredrag0n.appspot.com/o/Attachment-Posts%2FPost-2025-07-23-Username-enumeration-via-response-timing%2Fscreen-02.png?alt=media&token=83299eef-b901-4f8f-924b-ca687a83422b)
*Figure 2: Configuring Burp Suite Intruder for username enumeration*

![Response timing analysis in Burp Suite](https://firebasestorage.googleapis.com/v0/b/xredrag0n.appspot.com/o/Attachment-Posts%2FPost-2025-07-23-Username-enumeration-via-response-timing%2Fscreen-03.png?alt=media&token=fb56bec3-a075-4233-bbac-fec6cdb8fc26)
*Figure 3: Analyzing response times to identify valid usernames*

By comparing the response times for the `wiener` username and other payloads, we notice that one of the usernames (`ak`) produces a similar response time to the known valid user. This indicates that `ak` is a valid username.

![Identifying valid username based on response time](https://firebasestorage.googleapis.com/v0/b/xredrag0n.appspot.com/o/Attachment-Posts%2FPost-2025-07-23-Username-enumeration-via-response-timing%2Fscreen-04.png?alt=media&token=a4cd1d5a-eccc-4baa-8476-2a6b72c463ec)
*Figure 4: Valid username identified: ak*

**Valid username found:** `ak`

---

## Step 3: Brute-Forcing the Password

With the valid username (`ak`) identified, configure Burp Suite Intruder to brute-force the password. Set the username parameter to `ak`, use a random value for `X-Forwarded-For`, and iterate through the provided password list.

![Burp Suite Intruder setup for password brute-forcing](https://firebasestorage.googleapis.com/v0/b/xredrag0n.appspot.com/o/Attachment-Posts%2FPost-2025-07-23-Username-enumeration-via-response-timing%2Fscreen-04.png?alt=media&token=a4cd1d5a-eccc-4baa-8476-2a6b72c463ec)
*Figure 5: Brute-forcing the password for the valid username*

A successful login attempt will result in a 302 redirect status code.

![Successful login with valid credentials](https://firebasestorage.googleapis.com/v0/b/xredrag0n.appspot.com/o/Attachment-Posts%2FPost-2025-07-23-Username-enumeration-via-response-timing%2Fscreen-06.png?alt=media&token=5208c1de-400e-4ff7-a4fd-8f1b4960e6f9)
*Figure 6: Successful login detected by 302 redirect*

**Valid password found:** `7777777`

---

## Step 4: Logging In and Solving the Lab

With both the valid username and password (`ak`/`7777777`), log in to the application to access the account page and complete the lab.

![Lab solved: Account page accessed](https://firebasestorage.googleapis.com/v0/b/xredrag0n.appspot.com/o/Attachment-Posts%2FPost-2025-07-23-Username-enumeration-via-response-timing%2Fscreen-07.png?alt=media&token=e6380f11-0579-45e6-94a3-23c9792393c2)
*Figure 7: Lab solved - account page accessed with valid credentials*

---

## Resources Used
- [Bypass IP Restrictions with Burp Suite (Medium)](https://medium.com/r3d-buck3t/bypass-ip-restrictions-with-burp-suite-fb4c72ec8e9c)

---

## Risk Mitigation

**What is the risk?**
Username enumeration and brute-force vulnerabilities can allow attackers to compromise user accounts, leading to unauthorized access and potential data breaches.

**How to mitigate?**
- Implement consistent response times for authentication failures, regardless of the reason.
- Enforce account lockout or CAPTCHA after a limited number of failed attempts.
- Monitor and alert on suspicious login activity.
- Validate and restrict the use of headers like `X-Forwarded-For` to prevent IP spoofing.

> **Danger:** Username enumeration and brute-force vulnerabilities can lead to unauthorized access and data breaches if not properly mitigated.
{: .prompt-danger }

---

## Conclusion

This lab demonstrates the importance of securing authentication mechanisms against timing attacks and brute-force attempts. By understanding and mitigating these vulnerabilities, organizations can better protect their users and sensitive data.

<!-- comments -->
{% include comments.html %}
{% include analytics.html %}
