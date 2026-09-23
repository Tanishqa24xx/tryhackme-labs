# AI Threat Modelling

## Overview

Traditional threat modelling frameworks such as **STRIDE** have helped defenders identify security threats for decades. However, AI systems introduce new assets, behaviours, and failure modes that traditional frameworks were not designed to fully address.

AI threat modelling requires understanding:

* New AI-specific assets
* AI data supply chains
* Model behaviour and failure modes
* AI-specific attack techniques
* How existing frameworks such as STRIDE can be adapted for AI systems

---

# Task 1 — AI Threat Modelling

Traditional applications have familiar assets such as:

* Databases
* Source code
* Configuration files
* API keys
* User credentials

AI systems introduce additional assets that need to be protected.

## AI-Specific Assets

### 1. Training Data

Training data is used to teach the model.

Poisoning this data can corrupt the model's outputs at the source.

### 2. Model Weights / Parameters

Model weights are the numerical values that define what the model has learned.

These effectively **are the model**.

Stealing them can give an attacker a functional copy of the AI system, potentially representing months of compute and significant investment.

### 3. Embedding Vectors

Embedding vectors represent information in a form that can be used by AI systems.

Poisoning or manipulating embeddings can alter what information the model retrieves at query time.

### 4. System Prompts

Leaking system prompts can reveal:

* Security controls
* Business logic
* Guardrails

This can give attackers information about how to bypass restrictions.

### 5. Feature Stores

Feature stores contain preprocessed data that feeds real-time model inputs.

Tampering with features can change what the model sees during inference without modifying the model itself.

### 6. Model Registry / Artifacts

The model registry stores versions of trained models ready for deployment.

If the registry is compromised, an attacker could replace a legitimate model with a backdoored version.

---

# AI System Characteristics

## Non-Deterministic Behaviour

AI models, especially LLMs, can produce different outputs for the same input.

This makes:

* Testing
* Auditing
* Incident reproduction

more difficult than with deterministic software.

## The Black Box Problem

Many AI models, particularly deep neural networks, do not provide the same level of explainability as traditional application logic.

Defenders cannot simply trace the model's reasoning like a traditional code path.

Instead, security teams need to think in terms of:

* Input-output behaviour
* Failure modes
* Model behaviour

---

## Knowledge Check

### RAG Context Retrieval

**Question:** In a RAG-based system, which AI asset type is used to retrieve relevant context at query time?

**Answer:**

```text
Embedding vectors
```

### Model Registry

**Question:** An attacker gains access to MegaCorp's model registry and swaps the production model for a modified version. Which AI-specific asset has been compromised?

**Answer:**

```text
Model registry/Artifacts
```

---

# Task 2 — AI Data Supply Chain

Understanding an AI system also requires understanding how its assets are:

* Built
* Moved
* Consumed

AI systems introduce an **AI Data Supply Chain** in addition to the traditional software supply chain.

## 1. Data Collection

Sources can include:

* Public data
* Proprietary data
* Synthesized data
* Web scraping
* Purchased datasets
* Internal databases
* User-generated content
* Third-party providers

If an attacker can contribute to or influence one of these sources, they may gain a foothold in the AI supply chain.

## 2. Cleaning & Labelling

This stage includes:

* Preprocessing
* Annotation
* Quality control

Compromised labels can cause the model to learn incorrect associations.

## 3. Model Training

The model learns patterns from the prepared data.

Any malicious data that survives the earlier stages can become embedded into the model's weights.

Unlike a compromised software library that can potentially be patched, a poisoned model may require retraining from scratch, creating significant time and cost.

## 4. Validating & Packaging

The trained model is:

* Evaluated
* Versioned
* Converted into the required format
* Security scanned
* Stored in the model registry

A compromised registry could allow an attacker to replace the legitimate model with a backdoored version.

This could go unnoticed if the trigger inputs are absent from the validation dataset.

## 5. Inference

The model is deployed and serves predictions in production.

For RAG systems, this can also involve retrieving and augmenting the model's context.

## Key Difference — Time

One important difference between traditional software supply chains and AI data supply chains is **time**.

### MegaCorp Example

MegaCorp retrains its fraud detection system every month using new transaction data.

An attacker could:

1. Inject crafted transactions into the training pipeline.
2. Continue doing this over several months.
3. Gradually shift the model's decision boundary.
4. Create specific fraud patterns that become difficult for the system to detect.

The attack can therefore have a delayed effect rather than producing an immediate visible compromise.

---

# STRIDE Gaps in AI Systems

Traditional STRIDE categories can be adapted to AI, but AI introduces additional challenges.

| STRIDE Category            | AI-Specific Gap                                                               |
| -------------------------- | ----------------------------------------------------------------------------- |
| **Spoofing**               | AI-generated synthetic identities and deepfakes                               |
| **Tampering**              | Delayed effects of data poisoning on long-term model integrity                |
| **Repudiation**            | Lack of audit trails for distributed and difficult-to-explain model decisions |
| **Information Disclosure** | Model extraction through complex API query patterns                           |
| **Denial of Service**      | Model inversion and membership inference attacks that can exhaust resources   |
| **Elevation of Privilege** | Adversarial examples that manipulate system authorisation                     |

## Knowledge Check

### Training Data Poisoning

**Question:** An attacker injects crafted data points into a training pipeline over several months, gradually shifting the model's decision boundaries. At which supply chain stage does the attacker inject the malicious data?

**Answer:**

```text
Data Collection
```

### STRIDE — Data Poisoning

**Question:** Which STRIDE category is insufficient for capturing the delayed, diffuse effects of training data poisoning?

**Answer:**

```text
Tampering
```

---

# Adapting STRIDE for AI Systems

## Spoofing — Data Source Impersonation

Traditionally, spoofing involves an attacker forging credentials to impersonate a legitimate user or service.

In a RAG system, however, the model retrieves context from sources such as:

* Vector databases
* Document stores
* Web content
* External knowledge sources

The model may treat this retrieved information as trustworthy.

An attacker can inject malicious content into these sources and effectively spoof the knowledge the model relies on.

The model can then generate responses based on attacker-controlled information.

### Other Spoofing Threats

* **Model impersonation**

  * Deploying a look-alike API endpoint that mimics a legitimate AI service.

* **Adversary identity attacks**

  * Crafting inputs that fool AI-based identity verification systems such as facial recognition or voice authentication.

### MegaCorp Example

MegaCorp has a customer-facing chatbot that retrieves answers from an internal knowledge base using RAG.

An attacker injects fabricated policy documents into the knowledge base.

The chatbot then confidently provides incorrect information to customers because it treats the attacker-controlled content as legitimate.

---

# Tampering — Data Poisoning

Traditionally, tampering involves modifying data in transit or at rest, such as:

* Database records
* API responses
* Configuration files

With AI systems, an attacker can inject malicious data into the training pipeline.

The model learns incorrect patterns, but the effects may be:

* Delayed
* Embedded during training
* Only visible during inference

### Other Tampering Threats

* Model manipulation
* Directly modifying model weights
* Replacing models in the registry with backdoored versions
* Prompt injection
* Feature manipulation

### MITRE ATLAS

* **Data Poisoning — AML.T0020**
* **Backdoor ML Model — AML.T0018**

---

# Repudiation — Unexplainable Model Decisions

Traditionally, repudiation occurs when a user performs an action and later denies it because the system lacks sufficient logging or audit trails.

AI introduces another problem.

When a model makes consequential decisions, such as:

* Approving a loan
* Flagging a transaction
* Denying a claim

it may be difficult to determine exactly why the decision was made.

Without robust logging of:

* Inputs
* Outputs
* Model versions
* Retrieval context

reproducing or explaining a specific decision after the fact can be extremely difficult.

### Other Threats

* **Prompt & context volatility**

  * The full context behind an output may not be completely captured.

* **Model version ambiguity**

  * Without deployment logs, it may be impossible to identify which model state produced a particular output.

### MegaCorp Example

A regulator asks why the fraud detection system approved a suspicious transaction three weeks earlier.

The security team cannot determine:

* Which model version was running
* Which features were provided
* Which threshold triggered the approval

---

# Information Disclosure — Model Extraction

Traditional information disclosure can involve:

* Data breaches
* Insecure APIs
* Verbose error messages
* Improper access controls

AI systems introduce **model extraction**.

An attacker can systematically query a model's API and use the input-output pairs to reconstruct a functionally equivalent copy of the model.

The attacker does not necessarily need access to the model's internals.

Only the public-facing endpoint may be required.

Potential impact includes:

* Intellectual property loss
* Offline analysis of the extracted model
* Testing the extracted model for adversarial weaknesses

### Other Information Disclosure Threats

* **Training data extraction**

  * Crafting queries that cause the model to reproduce memorised training data.

* **System prompt leakage**

  * Revealing internal instructions, guardrails, and business logic.

* **Embedding inversion**

  * Attempting to reconstruct source documents from embedding vectors.

### MITRE ATLAS

* **Extract ML Model — AML.T0024**
* **Infer Training Data Membership — AML.T0025**

---

# Denial of Service — Inference Cost Exploitation

Traditional denial of service attacks flood systems with traffic to exhaust resources and make the service unavailable.

AI inference can introduce another problem because inference can be significantly more expensive than traditional API calls.

Cloud-based AI services may charge based on:

* Tokens
* Queries
* Compute usage

An attacker can therefore cause financial damage without taking the service offline.

### Denial of Wallet

An attacker can:

* Generate large volumes of expensive queries
* Send long prompts
* Request maximum-length outputs

This can drive operational costs to unsustainable levels.

### Other Threats

* GPU resource exhaustion
* Sponge examples
* Training pipeline disruption

### OWASP LLM Top 10

**LLM10:2025 — Unbounded Consumption**

---

# Elevation of Privilege — Jailbreaking & Excessive Agency

Traditionally, privilege escalation means gaining higher-level access or capabilities than intended.

In AI systems, an attacker may craft prompts that cause an LLM to ignore:

* Safety guidelines
* Content policies
* Behavioural restrictions

The attacker does not necessarily obtain root access to a server.

Instead, they gain access to capabilities that the model was instructed to restrict.

## Other AI Privilege Threats

### Excessive Agency

An AI system may have more tool permissions than appropriate for its context.

A compromised chatbot could potentially gain access to:

* Internal databases
* Email systems
* Code execution

### Tool Use Exploitation

An attacker manipulates an agentic AI into using its tools for unintended purposes.

Examples include:

* Web browsing
* File writing
* API calls

### Cross-Plugin Escalation

An attacker compromises the input to one plugin and uses it to influence the model's behaviour with another, more privileged plugin.

### OWASP LLM Top 10

**LLM06:2025 — Excessive Agency**

---

# What STRIDE Still Misses

Even after adapting STRIDE for AI systems, some AI-specific concerns remain outside the traditional framework, including:

* Adversarial examples
* Model bias
* Fairness issues
* Emergent behaviours

<img height="300" alt="image" src="https://github.com/user-attachments/assets/669a14c8-625a-4808-91ad-290cc9892823" />


---

## Knowledge Check

### Information Disclosure

**Question:** What is the primary AI-specific manifestation of Information Disclosure in the STRIDE-AI mapping?

**Answer:**

```text
Model extraction
```

### Jailbreaking

**Question:** An attacker crafts prompts that cause an LLM to bypass its safety guidelines and content restrictions. Which STRIDE category does this map to?

**Answer:**

```text
Elevation of privilege
```

### Excessive Agency

**Question:** Which OWASP LLM Top 10 (2025) entry addresses the risks of AI systems being granted too many permissions or too much autonomy?

**Answer:**

```text
LLM06:2025 — Excessive Agency
```

### Denial of Wallet

**Question:** An attacker drives a monthly inference bill from $15,000 to $180,000 without taking the service offline. What is this type of attack commonly called?

**Answer:**

```text
Denial of wallet
```

---

# Task 5 — MITRE ATLAS

## What is MITRE ATLAS?

**MITRE ATLAS** stands for:

> **Adversarial Threat Landscape for Artificial-Intelligence Systems**

It is a knowledge base of adversary tactics and techniques targeting AI and ML systems.

It can be viewed as MITRE ATT&CK's AI-focused counterpart.

<img height="300" alt="image" src="https://github.com/user-attachments/assets/b87d46d2-b69b-44c4-aff3-5b71b85a7c9e" />

---

## Key Techniques

### Data Poisoning

**AML.T0020**

Maps to:

```text
STRIDE → Tampering
```

### Model Extraction

**AML.T0024**

Maps to:

```text
STRIDE → Information Disclosure
```

### Evade ML Model

**AML.T0015**

This can map to multiple STRIDE categories depending on the context:

* Tampering
* Spoofing
* Elevation of Privilege

Adversaries may use this technique to:

* Evade malware detection
* Bypass content filters
* Cause misclassification in downstream tasks

### LLM Prompt Injection

**AML.T0051**

Two forms:

* **Direct injection**

  * The user directly crafts malicious input in the chat interface.

* **Indirect injection**

  * Malicious instructions are embedded in content the model retrieves or processes, such as documents in a RAG pipeline.

Maps to:

```text
STRIDE → Tampering
```

### Backdoor ML Model

**AML.T0018**

<img height="300" alt="image" src="https://github.com/user-attachments/assets/300d450a-0f2a-42dc-a383-d404108c4ebd" />


---

# ATLAS Case Studies

## ShadowRay

**AML.CS0023**

Attackers exploited vulnerabilities in Ray, a framework used for distributed AI workloads, to compromise AI training infrastructure in the wild.

This demonstrates that AI supply chain attacks can affect production systems.

Reference:

[MITRE ATLAS — ShadowRay](https://atlas.mitre.org/studies/AML.CS0023)

---

## Morris II Worm

**AML.CS0024**

Researchers demonstrated a self-replicating prompt injection worm that could spread between AI agents through RAG-based email systems.

The worm:

* Injected its payload into the model's context
* Extracted PII
* Automatically propagated to other agents

Reference:

[MITRE ATLAS — Morris II Worm](https://atlas.mitre.org/studies/AML.CS0024)

---

## ATLAS Knowledge Check

### ATLAS Acronym

**Question:** What does the acronym ATLAS stand for?

**Answer:**

```text
Adversarial Threat Landscape for Artificial-Intelligence Systems
```

### ATLAS Case Study

**Question:** Which ATLAS case study described a self-replicating prompt injection worm that spread between AI agents via RAG email systems?

**Answer:**

```text
Morris II
```

### Model Extraction

**Question:** What is the ATLAS technique ID for Model Extraction?

**Answer:**

```text
AML.T0024
```

---

# OWASP LLM Top 10

<img height="300" alt="image" src="https://github.com/user-attachments/assets/92ea06cf-2c8a-4856-9f86-68e245c2ea38" />
<img height="300" alt="image" src="https://github.com/user-attachments/assets/cd234140-52f5-412d-86df-b7e593f99039" />



## Reading the Framework Like a Defender

The OWASP LLM Top 10 can be approached from two directions.

### Risk → Component

Example:

> "Prompt injection — where does it live?"

Look at the relevant row and identify the components affected.

Prompt injection primarily targets:

* The inference endpoint
* The RAG pipeline

These components therefore need controls such as:

* Input validation
* Prompt boundary enforcement

### Component → Risk

Example:

> "We're deploying a vector database for RAG. What risks does it carry?"

Look at where the vector database appears in the framework.

The vector database can appear under:

* **LLM01** — Indirect Prompt Injection
* **LLM08** — Embedding Weaknesses
* **LLM09** — Misinformation from Stale Sources

These become part of the security assessment scope for that component.

<img height="300" alt="image" src="https://github.com/user-attachments/assets/78230e68-41cc-4bea-8776-d5d2ca115cb4" />

---

# OWASP Knowledge Check

### LLM Inference Endpoint

**Question:** How many OWASP LLM Top 10 entries affect the LLM Inference Endpoint?

**Answer:**

```text
7
```

### Improper Output Handling

**Question:** An organisation notices that its chatbot is rendering LLM output directly in the browser without sanitisation. Which OWASP entry does this fall under?

**Answer:**

```text
Improper output handling
```

### LLM03

**Question:** Which component in a typical LLM architecture is the primary one that needs hardening against data and model supply chain risks (LLM03)?

**Answer:**

```text
Training pipeline
```

<img height="300" alt="image" src="https://github.com/user-attachments/assets/28e5fd8d-7821-4461-ac2a-2e18be744cff" />

---

# Key Takeaways

* Traditional frameworks such as **STRIDE** do not fully cover AI-specific threats.
* AI introduces new assets such as:

  * Training data
  * Model weights
  * Embeddings
  * System prompts
  * Feature stores
  * Model registries
* AI data supply chains introduce security risks across the entire lifecycle, from data collection to inference.
* **MITRE ATLAS** provides a framework for understanding adversarial techniques targeting AI and ML systems.
* **Model extraction**, **data poisoning**, **prompt injection**, and **model backdooring** are important AI-specific threats.
* AI systems also introduce security concerns around **inference costs**, **excessive agency**, and **unexplainable decisions**.
* The **OWASP LLM Top 10** can be used to assess risks based on both the affected component and the specific risk.
* Effective AI threat modelling requires looking beyond traditional application security and considering how data, models, prompts, and AI-specific behaviour interact.

