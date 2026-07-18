#  OWASP Top 10 2025: Application Design Flaws

**AS02: Security Misconfigurations**
**AS03: Software Supply Chain Failures**
**AS04: Cryptographic Failures**
**AS06: Insecure Design**


## AS02: Security Misconfigurations

- Occur when systems, servers, or apps are deployed with unsafe defaults, incomplete settings, or exposed services — creating easy entry points for attackers.
- **What can attackers do?** Expose sensitive data, enable privilege escalation, and gain a foothold in the system. Modern apps have complex stacks and use cloud services and third-party APIs — any single exposed admin panel, open storage bucket, or misconfigured config can compromise the entire system.
- **Example:** Uber exposed a backup AWS S3 bucket that was publicly accessible — a deployment mistake.
- **Common Patterns:**
  1. Default credentials or weak passwords left unchanged.
  2. Unnecessary services or endpoints exposed to the internet.
  3. Misconfigured cloud storage or permissions (S3, Azure Blob, GCP buckets).
  4. Unrestricted API access or missing authentication/authorization.
  5. Verbose error messages exposing stack traces or system details.
  6. Outdated software, frameworks, or containers with known vulnerabilities.
  7. Exposed AI/ML endpoints without proper access controls.
- **Prevention:**
  1. Harden default configurations and remove unused features or services.
  2. Enforce strong authentication and least privilege across all systems.
  3. Limit network exposure and segment sensitive resources.
  4. Keep software, frameworks, and containers up to date with patches.
  5. Hide stack traces and system information from error messages.
  6. Audit cloud configurations and permissions regularly.
  7. Secure AI endpoints and automation services with proper access controls and monitoring.
  8. Integrate configuration reviews and automated security checks into the deployment pipeline.
- **Challenge:** navigate to `10.48.184.5:5002`. Developers left too many traces in their User Management APIs.
  1. Navigated to `<ip>:<port>` — endpoint exposed.
  2. Tried `http://10.48.184.5:5002/api/user/123`.
  3. Tried `http://api.10.48.184.5:5002/user/123`.
  4. Worked: `http://10.48.184.5:5002/api/user/{123}`

---

## AS03: Software Supply Chain Failures

- Occur when apps rely on components, libraries, services, or models that are compromised, outdated, or improperly verified — attackers exploit these weak links to inject malicious code, bypass security, or steal sensitive data.
- **Why it matters:** modern apps are built from many third-party packages, APIs, and AI models. One compromised dependency can compromise the entire system, letting an attacker gain access without touching the app's own code.
- Supply chain attacks can be automated and distributed, making them hard to detect and highly damaging.
- **Example:** SolarWinds Orion — attackers inserted malicious code into a trusted update, exploiting a flaw in the software update build, verification, and distribution process.
- **Common Patterns:**
  1. Using unverified or unmaintained libraries and dependencies.
  2. Automatically installing updates without verification.
  3. Over-reliance on third-party AI models without monitoring or auditing.
  4. Insecure build pipelines or CI/CD processes that allow tampering.
  5. Poor license or provenance tracking for components.
  6. Lack of monitoring for vulnerabilities in dependencies after deployment.
- **Protection Measures:**
  1. Verify all third-party components, libraries, and AI models before use.
  2. Monitor and patch dependencies regularly.
  3. Sign, verify, and audit software updates and packages.
  4. Lock down CI/CD pipelines and build processes to prevent tampering.
  5. Track provenance and licensing for all dependencies.
  6. Implement runtime monitoring for unusual behavior from dependencies or AI components.
  7. Integrate supply chain threat modeling into the SDLC, including testing, deployment, and update workflows.
- **Challenge:** navigate to `10.48.184.5:5003`. The code is outdated and imports an old `lib/vulnerable_utils.py` component.
  1. Used Burp Repeater.
  2. Sent a POST request.
  3. Set the `Content-Type` header to `application/json` based on the error returned.
  4. Sent a JSON object with `data` set to `debug`, then checked the code for that path.
  5. Flag: `THM{SUPPLY_CH41N_VULN3R4B1L1TY}`

---

## AS04: Cryptographic Failures

- Occur when encryption is used incorrectly or not at all — includes weak algorithms, hard-coded keys, poor key handling, and unencrypted sensitive data.
- **Why it matters:** web apps need cryptography everywhere (protecting network traffic, securing stored data, verifying identities, safeguarding secrets). If these controls fail, sensitive data (passwords, tokens, personal info) is exposed, potentially leading to account takeovers or full breaches.
- Flaws can be exploited via MITM attacks, brute-force attacks, or by discovering unprotected secrets.
- **Common Patterns:**
  1. Using deprecated or weak algorithms like MD5, SHA-1, or ECB mode.
  2. Hard-coded secrets in code or configuration.
  3. Poor key rotation or management practices.
  4. Lack of encryption for sensitive data at rest or in transit.
  5. Self-signed or invalid TLS certificates.
  6. Using AI/ML systems without proper secret handling for model parameters or sensitive inputs.
- **Prevention:**
  1. Use strong, modern algorithms such as AES-GCM or ChaCha20-Poly1305, or enforce TLS 1.3 with valid certificates.
  2. Use secure key management services like Azure Key Vault, AWS KMS, or HashiCorp Vault.
  3. Rotate secrets and keys regularly, following defined crypto periods.
  4. Document and enforce policies and standard operating procedures for key lifecycle management.
  5. Maintain a complete inventory of certificates, keys, and their owners.
  6. Ensure AI models and automation agents never expose unencrypted secrets or sensitive data.
- **Lab Steps:**
  1. Page showed an encrypted document. Viewed the page source to check if a key existed there.
  2. Revealed an external JS script that was loaded. Visited it: `view-source:http://10.48.131.38:5004/static/js/decrypt.js`
  3. Found the `SECRET_KEY` constant set to `my-secret-key-16`, with the encryption mode as ECB and key size 128.
  4. Since ECB uses the same key for encryption and decryption, used the encrypted document with a written program to recover the key.

---

## AS06: Insecure Design

- Occurs when flawed logic or architecture is built into the system from the start — from skipped threat modeling, missing design requirements or reviews, or accidental errors. AI-assisted or AI-generated code is often wrongly assumed to be safe or flaw-free.
- **Example:** Clubhouse — assumed users would only interact through the mobile app, but the backend API had no proper authentication, allowing anyone to query user data, room info, and private conversations.
- **Why it matters:** insecure design can't be patched like a bug, since it's built into the workflow, logic, and trust boundaries — fixing it means rethinking how the system and AI make decisions.
- **Common Insecure Designs in 2025:**
  1. Weak business logic controls, like recovery or approval flows.
  2. Flawed assumptions about user or model behavior.
  3. AI components with unchecked authority or access.
  4. Missing guardrails for LLMs and automation agents.
  5. Test or debug bypasses left in production.
  6. No consistent abuse-case review or AI threat modeling.
- **Insecure Design in the AI Era:**
  1. **Example:** prompt injection occurs when user input is blended with system prompts, allowing attackers to hijack context or extract hidden data.
  2. Blind trust in a model creates fragile systems, since actions are taken on AI decisions without validation or oversight — human review is necessary.
- **How to Design Securely:**
  1. Treat every model as untrusted until proven otherwise.
  2. Validate and filter all model inputs and outputs to ensure accuracy and integrity.
  3. Separate system prompts from user content.
  4. Keep sensitive data out of prompts unless absolutely needed, and protect it with strict controls.
  5. Require human review for high-risk AI actions.
  6. Log model provenance, monitor behavior, and apply differential privacy for sensitive data.
  7. Include AI-specific threat modeling for prompt attacks, inference risks, agent misuse, and supply chain compromise throughout the design process.
  8. Build threat modeling into every stage of development, not just at the start.
  9. Define clear security requirements for each feature before implementation.
  10. Apply the principle of least privilege across users, APIs, and services.
  11. Ensure proper authentication, authorization, and session management across the system.
  12. Keep dependencies, third-party components, and supply chain sources verified and up to date.
  13. Continuously monitor and test the system for logic flaws, abuse paths, and emergent risks as new features or AI components are added.
- **Challenge:** navigate to `10.48.131.38:5005`. Have they assumed that only mobile devices can access it?
  1. `gobuster dir -u http://10.48.188.96:5005/api -w /usr/share/wordlists/dirb/common.txt -x php,txt,html,js` → revealed `/users`
  2. Visited `http://10.48.188.96:5005/api/users` → found admin, user1, and user2 emails, names, and roles.
  3. Visited each one separately — no additional info found.
  4. `gobuster dir -u http://10.48.188.96:5005/api/users -w /usr/share/wordlists/dirb/common.txt -x php,txt,html,js` → revealed `/admin`
  5. `gobuster dir -u http://10.48.188.96:5005/api/messages -w /usr/share/wordlists/dirb/common.txt -x php,txt,html,js` → revealed `/admin`
  6. Visited that endpoint in the browser and found the flag in the admin panel access key.

<img height="400" alt="image" src="https://github.com/user-attachments/assets/923b7061-7df4-4ff2-aeaf-9f7c1c22039d" />


