# OWASP Top 10 2025: Insecure Data Handling

**A04: Cryptographic Failures**
**A05: Injection**
**A08: Software or Data Integrity Failures**

## A04: Cryptographic Failures

- Occur when sensitive data isn't adequately protected due to lack of encryption, faulty implementation, or insufficient security measures. E.g.: storing passwords without hashing, using outdated or weak algorithms (MD5, SHA1, DES), exposing encryption keys, or failing to secure data during transmission.
- **Prevention:**
  1. Choose strong, modern algorithms and implement them properly.
  2. Sensitive info like passwords must be hashed using robust, slow hashing functions such as bcrypt, scrypt, or Argon2.
  3. When encrypting data, avoid creating your own algorithms — rely on trusted, industry-standard libraries.
  4. Never embed access credentials (e.g. to a third-party service) in source code, config files, or repos. Instead, use secure key management systems or environments designed for storing secrets.
- **Decrypt encrypted notes:**
  1. **Vulnerability:** the encryption key value could be easily brute-forced.
  2. **Fix:** use industry-standard encryption (AES-256-GCM) with proper key management.

---

## A05: Injection

- Occurs when an application takes user input and mishandles it. Instead of processing the input securely, it passes it directly into a system that can execute commands or queries, such as a database, shell, templating engine, or API.
- **SQL injection:** on a login page, the web app fails to sanitize user input and instead uses it directly to construct the query.
- **Injection examples:** SQL injection, command injection, AI prompt injection, Server-Side Template Injection (SSTI).
- **Prevention:**
  1. Always treat user input as untrusted — enforce input validation and sanitization.
  2. For SQL queries, use prepared statements and parameterized queries instead of building queries through string concatenation.
  3. For OS commands, avoid functions that pass input directly to the system shell. Instead, rely on safe APIs and processes that don't invoke a shell at all.
- **Abused an application's ability to render dynamic content to retrieve a flag (SSTI attack):**
  1. **Vulnerability:** raw user input (user-controlled strings) inside the Jinja2 template is evaluated as a template. When not sandboxed, Jinja2 templates can access Python objects, allowing attackers to reach `config`, `request`, `cycler`, `joiner`, and `lipsum`. Attackers can also leverage these objects to access host resources.
  2. **Fix:** input validation and sanitization.
  3. Explored using `{{7*7}}` and `{{config.items()}}`.
  4. `{{request.__dict__}}`
  5. `{{ request.application.__globals__.__builtins__.open('flag.txt').read() }}`
  - Reference: Jinja2 SSTI — HackTricks

---

## A08: Software or Data Integrity Failures

- Occur when an app relies on code, updates, or data it assumes are safe without verifying their authenticity, integrity, or origin.
- E.g.: trusting software updates without verification, loading scripts or config files from untrusted sources, failing to validate data that impacts app logic, or accepting data (binaries/templates/JSON files) without confirming whether it has been altered.
- **Avoiding:**
  1. Establish trust boundaries.
  2. Apps should never assume that code, updates, or key pieces of data are legitimate and automatically trusted — always verify their integrity.
  3. Methods to use: cryptographic checks (checksums) for update packages.
  4. For apps, integrity and trust boundaries should be enforced within build processes such as CI/CD.
- **Deserialization attack in Python:**
  1. Example code:
```python
     import pickle
     import base64

     class Malicious:
         def __reduce__(self):
             # Return a tuple: (callable, args)
             # This will execute: open('flag.txt').read()
             return (eval, ("open('flag.txt').read()",))

     # Generate and encode the payload
     payload = pickle.dumps(Malicious())
     encoded = base64.b64encode(payload).decode()
     print(encoded)
```
  2. **Vulnerability:** no integrity verification, unsafe deserialization, trust boundary violation, no input validation. The `pickle` module allowed arbitrary code execution via the `__reduce__` method during deserialization.
  3. **Fix:** use safe serialization formats, verify digital signatures before deserializing, whitelist allowed object types, use restricted unpicklers or sandboxed environments, and never deserialize untrusted data.

