# Intro to Threat Emulation

## Overview

Threat emulation is an intelligence-driven way of testing security defences by replicating the tactics, techniques, and procedures (TTPs) used by real-world adversaries.

Instead of assuming that security controls work as expected, threat emulation gives defenders an adversary's perspective of their own environment. This helps identify and fix security gaps before a real attacker finds them.

---

## Threat Emulation

Threat emulation is an intelligence-driven impersonation of real-world attack scenarios and TTPs in a controlled environment to test, assess, and improve an organisation's security defences and response capabilities.

The goal is to behave as the adversary would and identify security gaps before attackers can exploit them.

## Types of Emulation Operations
1. Blind operation
   *Red team engagement is unannounced to the wider defensive security team.
2. Non-blind operation
   * All relevant security teams are aware of the exercise.
   * Encourages knowledge sharing throughout the exercise.

---

## Threat Emulation vs Threat Simulation

### Threat Emulation
Accurately replicates the behaviour of a specific adversary based on real threat intelligence.

### Threat Simulation
Represents adversary behaviour using predefined and automated attack patterns.

These patterns can combine TTPs from one or more threat groups without exactly imitating a particular adversary.

---

## Emulation Spectrum
1. Atomic Testing
   * Runs individual, isolated technique-level tests.
   * Tests are mapped to MITRE ATT&CK.
   * Highly focused and repeatable.
     
2. Adversary Emulation
   * Chains multiple TTPs together.
   * Replicates a specific threat actor's campaign end-to-end.
   * Can cover the full attack path from initial access through impact.
     
3. Purple Teaming
   * Red and blue teams work together during the exercise.
   * Techniques are executed while defenders check detections in real time.

---

## Key Concepts

### Real-World Threats
Threat emulation is based on actual breaches, APTs, and campaigns using sources such as:

* MITRE ATT&CK
* Cyber threat intelligence
* Real-world incident research

### Behaviour-Focused
Focuses on adversary behaviour rather than static signatures, making defences more robust against evolving threats.

### Transparent
Activities are disclosed between teams so that the overall security posture can be improved rather than teams working in isolation.

### Collaborative
Red and blue teams work toward the common goal of improving organisational security rather than competing against each other.

### Repeatable
Emulation tasks can be automated and repeated, creating a baseline for continuous practical security assessment.

---

## Framework — MITRE ATT&CK

MITRE ATT&CK is an industry-standard knowledge base of adversarial TTPs observed in real-world attacks and breaches.

It provides a common language for threat emulation.

---

## Threat Emulation Resources

### Atomic Red Team
Atomic Red Team provides a library of emulation tests.

Each test, called an atomic, is a small focused unit that executes a single technique and generates telemetry that can be used to validate and improve detections.

Atomics are mapped to MITRE ATT&CK.

Basic workflow:

1. Choose an attack technique.
2. Choose a test for that technique.
3. Analyse whether the activity is detected.
4. Identify weaknesses in the defence.
5. Improve the relevant controls.
6. Re-test.

### CITD Adversary Emulation Library
An open-source adversary emulation plan library containing ready-made plans that organisations can use to evaluate their capabilities against real-world threats.

Two approaches are offered:

* Full Emulation
- Comprehensive emulation of a particular adversary.
- Can cover the attack path from initial access to exfiltration.
  
* Micro Emulation
- Focuses on specific behaviours across multiple adversaries.
- Examples include file access or process injection techniques.

---

## Methodology — TIBER-EU

TIBER-EU is a Threat Intelligence-based Ethical Red Teaming framework.

It provides a structured three-phase process for intelligence-led emulation testing against critical live production systems.

1. Preparation Phase
* Security teams are established.
* Scope is defined.
* Management approval is obtained.
* Planning and procurement are completed before testing begins.
  
2. Testing Phase
* The threat intelligence team produces a detailed report of threat areas and attack scenarios.
* The red team uses this intelligence to conduct emulation against critical systems.
* The blue team assesses how the defences perform.
  
3. Closure Phase

Each team produces analysis covering:

* What was tested
* Findings
* Recommendations
* Technical controls
* Policies
* Security awareness training

---

## Threat Emulation Process 1

### FIN6 & FIN7 Tools

Question: There are 3 software tools used by FIN6 & FIN7. Identify them.

Process:
* Researched the tools associated with FIN6 and FIN7.
* Found:
  - AdFind
  - Cobalt Strike
  - Mimikatz

Answer: AdFind, Cobalt Strike, Mimikatz

### Existing vs Custom Tools

Question: Which factor is considered when analysing whether to use existing or custom tools during emulation?

Answer: TTP complexity

---

## Threat Emulation Process 2

### Emulation Plan

Question: What is the emulation plan component that determines which activities are to be conducted?

Answer: Scope

### Carbon Spider — Persistence

Question: While emulating Carbon Spider, what would be used to achieve persistence?

Process:
* Researched FIN7's MITRE ATT&CK profile.
* Found that FIN7 has used scheduled tasks for persistence.
* Research also showed FIN7 using OpenSSH to establish persistence.

Reference used:
* MITRE ATT&CK — FIN7

### Carbon Spider — POS Malware

Question: What POS malware has Carbon Spider been known to use to harvest credit card data?

Answer: Pillowmint

Pillowmint is a point-of-sale malware associated with FIN7 and designed to capture credit card information.

### Carbon Spider — Ransomware

Question: What is Carbon Spider's ransomware program?

Process:
* Checked the MITRE ATT&CK information for FIN7.
* Found that FIN7 has encrypted virtual disk volumes on ESXi servers using a version of DarkSide ransomware.

Answer: DarkSide

### Reaper — Initial Access

Question: Assuming you are emulating Reaper, what would you use to achieve Initial Access?

Answer: Drive-by Compromise

### Reaper — Command and Control

Question: Reaper's Command and Control plan utilises DOGCALL malware. Which platform would be used to deploy it?

Answer: Dropbox

### Reaper — Defence Evasion

Question: Which defence evasion technique would you use to hide information?

Answer: Steganography

### Reaper — Zero-Day

Question: The initial zero-day abused by Reaper was on which software?

Answer:

Adobe Flash

---

## Threat Emulation Process 3

### Blue Team Activities

During threat emulation, blue team activities can include:

* Log analysis
* Event log evaluation
* Network traffic monitoring

### Analysing Detection Results

The observation of each emulated TTP can generally fall into three categories:

Result  Meaning  
Detected and blocked    Defence worked as expected  
Detected but not blocked    Visibility exists, but response needs tuning  
Not detected    A detection gap exists and needs to be addressed  

### Structured Emulation Report

A good emulation report should contain:

Exercise Summary  
  * Adversary being emulated
  * TTPs tested
    
Findings  
  * Detected activity
  * Blocked activity
  * Missed activity
  * Supporting evidence  

### Gaps Identified

Document weaknesses discovered during the exercise.

### Recommendations

Provide specific and actionable improvements.

### Re-validation Plan

Define how the improvements will be tested again.

### APT Defence  
Command and Scripting Techniques

Question: While defending against APTs, what mitigation measure would you use to prevent command and scripting techniques?

Answer:

Execution prevention

### Ransomware Data Loss

Question: How would you mitigate against data loss through ransomware encryption?

Answer:

Data backup

### Data Exfiltration

Question: What is the most efficient method used for data exfiltration?

Answer:

Uploading files to cloud storage

### First Worm-Like Ransomware

Question: What was the first ransomware that leveraged worm-like capabilities?

Answer:

WannaCry

Flag: Round 1 flag found.

### Web Attack Detection  
SQL Injection

Question: When emulating an SQL injection attack, what indicators in web server logs can suggest it was successful?

Answer:

Presence of SQL syntax in logs

### Brute-Force Detection

Question: What kind of web server logs would effectively indicate brute-force attacks during emulation and detection?

Answer:

Repeated login attempts from the same IP address

### File Inclusion Detection

Question: Which artefacts in web server logs could indicate file inclusion attacks?

Answer:

Presence of path traversal syntax in logs

### Outlook Web App Exploitation

Question: Which group was responsible for breaches through exploitation of the Outlook Web App?

Answer:

HAFNIUM

### Key Takeaways

- Threat emulation uses real-world threat intelligence to reproduce adversary behaviour.  
- Threat emulation focuses on accurately replicating a specific adversary, while threat simulation can use predefined attack patterns without exactly copying one threat actor.  
- MITRE ATT&CK provides a common language for describing adversary TTPs.  
- Atomic testing provides small, repeatable technique-level tests.  
- Adversary emulation chains TTPs together to reproduce a campaign.  
- Purple teaming combines offensive and defensive activity during testing.  
- Threat emulation is not only about whether an attack succeeds — it also measures whether defenders detect, block, investigate, and respond to the activity.  
- Findings should feed directly into remediation and re-validation.
