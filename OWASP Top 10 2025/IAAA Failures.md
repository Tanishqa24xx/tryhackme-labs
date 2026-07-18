# OWASP Top 10 2025: IAAA Failures

**A01: Broken Access Control**
**A07: Authentication Failures**
**A09: Logging and Alerting Failures**

## IAAA Overview

- **IAAA** = Identification (a unique account, e.g. user ID/email), Authentication (proving identity, e.g. password/OTPs/passkeys), Authorization (what an identity is allowed to do), and Accountability (recording and alerting on who did what, when, and from where).
- Ensures only authorized users can access the system, and that their actions can be tracked.
- **OWASP** = Open Web Application Security Project

---

## A01: Broken Access Control

- Occurs when the server doesn't properly enforce who can access what on every request.
- Common occurrence: **IDOR (Insecure Direct Object Reference)**.
- Changing an ID, such as `?id=7` to `?id=6`, lets you see someone else's data — access control is broken.
- Can lead to horizontal or vertical privilege escalation.
- Started the static site attached to this task and experimented with the `accountID` value in the URL, to identify which user has more than $1 million in their account:
  1. Changed the `?id=5` parameter in the URL.
  2. `id=7`, username/flag = `THM{Found.the.Millionare!}`
  3. **Vulnerability Detected:** IDOR — by simply changing the `id` parameter in the URL, you can access another user's account info without proper authorization.
  4. **How to fix this vulnerability:**
     - Implement proper authentication and authorization checks.
     - Verify that the authenticated user has permission to access the requested resource.
     - Use indirect references or encrypted tokens instead of predictable IDs.
     - Log and monitor access attempts to sensitive resources.

---

## A07: Authentication Failures

- Occurs when an app can't reliably verify or bind a user's identity.
- Common issues: username enumeration, weak/guessable passwords (no lockout, no rate limits).
- Logic flaws in the login or registration flow.
- Insecure cookie or session handling.
- Broke into the admin's account by registering as `aDmin`.
- Flag = `THM{Account.confusion.FTW!}`

---

## A09: Logging and Alerting Failures

- No record or alert of events means the defender cannot detect or investigate incidents.
- Failures include: missing authentication events, vague error logs, no alerting on brute-force attempts or privilege changes, short retention periods, and logs stored where attackers can tamper with them.
- Performed an investigation of the application:
  1. Reviewed the page showing event logs and analyzed the events' status codes. For `401 Unauthorized` access, found an alert for a brute-force attack.
  2. Multiple failed attempts were detected.
  3. Found the IP origin, along with usernames and passwords used.
  4. Endpoint accessed: `/supersecretadminstuff`

---

## Ideas for Patching

1. **A01: Broken Access Control** — enforce server-side checks for every request.
2. **A07: Authentication Failures** — enforce unique indexes on the canonical form, rate-limit/lock out brute force attempts, and rotate sessions on password or privilege changes.
3. **A09: Logging and Alerting Failures** — log the full auth lifecycle (failures/successes, password/2FA/role changes, admin actions), centralize logs off-host with proper retention, and alert on anomalies (e.g. brute-force bursts, privilege elevation).

