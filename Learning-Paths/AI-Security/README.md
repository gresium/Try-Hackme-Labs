# AI Security

**Status:** ✅ Completed  
**Platform:** TryHackMe  
**Level:** Intermediate → Advanced  

---

## Overview

The AI Security path covers one of the most rapidly evolving attack surfaces in 
cybersecurity: artificial intelligence systems and large language model (LLM) 
deployments. As AI is integrated into applications, development pipelines, 
customer service systems, and security tooling, a new category of vulnerabilities 
has emerged — one that traditional security frameworks were not designed to address.

This path approaches AI security from both directions: how AI is used as a defensive 
tool in cybersecurity, and how AI systems themselves can be attacked, manipulated, 
and exploited. For anyone working in offensive security, understanding LLM attack 
surfaces is becoming as essential as understanding web application vulnerabilities.

---

## Modules & Rooms

### 🔹 Introduction to AI Security
- The AI/ML pipeline: data collection → training → deployment → inference — 
  and where each stage introduces security risks
- The difference between traditional software vulnerabilities and AI-specific ones: 
  why you cannot patch a language model the same way you patch a web application
- Threat landscape overview: prompt injection, model theft, training data poisoning, 
  adversarial examples, and supply chain attacks against AI systems
- The AI security triad: Confidentiality (protecting training data and model weights), 
  Integrity (ensuring model behaviour is not manipulated), 
  Availability (preventing DoS against inference infrastructure)

### 🔹 Prompt Injection
The most critical and widespread AI vulnerability class — directly analogous to 
SQL injection but targeting the instruction-following behaviour of language models:

- **Direct Prompt Injection** — Crafting inputs that override the system prompt, 
  causing the model to ignore its instructions and execute attacker-specified behaviour. 
  Techniques: role override ("ignore all previous instructions"), 
  instruction smuggling, context confusion attacks
- **Indirect Prompt Injection** — Injecting malicious instructions into data sources 
  that the AI will later process: documents, web pages, emails, database entries. 
  When the AI retrieves and processes this content, it executes the injected instructions
  without the user or operator being aware
- **Multi-turn Injection** — Building up a manipulated context across multiple 
  conversation turns to gradually shift model behaviour
- **Stored Prompt Injection** — Persistent injections that affect every user 
  who triggers a specific retrieval path
- Real-world examples: AI assistants that can be made to exfiltrate conversation 
  history, modify responses, or perform unauthorised actions through injected instructions 
  in retrieved documents

### 🔹 Jailbreaking Techniques
- What alignment training is and why it creates exploitable patterns rather than 
  genuine restrictions
- **Role-playing attacks** — Convincing the model to adopt a persona that does not 
  have the same restrictions ("DAN", "evil twin", "unrestricted AI" patterns)
- **Hypothetical framing** — Asking for harmful information in fictional, 
  academic, or hypothetical contexts that the model treats as safe
- **Token smuggling** — Encoding restricted content in formats the safety 
  classifier does not check: pig Latin, base64, character substitution, 
  language switching
- **Many-shot jailbreaking** — Using long context windows to provide many 
  examples of the model "complying" with restricted requests, establishing a 
  false precedent the model follows in the final turn
- **Gradient-based adversarial prompts** — Automatically generated strings 
  that reliably bypass alignment (GCG attacks, AutoDAN)
- Why jailbreaking is a security research concern: deployed AI systems are 
  used for sensitive operations where behavioural bypasses have real-world consequences

### 🔹 LLM Attack Surface
- How LLMs are integrated into production applications: 
  API wrappers, RAG systems, autonomous agents, plugin architectures
- Trust boundary analysis: where does the model trust user input, 
  system prompt, retrieved context, tool outputs — and how each trust boundary 
  can be violated
- **LLM as a proxy** — When an AI agent can make API calls, browse the web, 
  or execute code, prompt injection becomes RCE or data exfiltration
- **System prompt extraction** — Techniques for leaking confidential 
  system prompts through careful questioning and context manipulation
- Insecure output handling: when model output is passed directly to 
  downstream systems (SQL queries, shell commands, HTML rendering) without 
  sanitisation — prompt injection becomes traditional injection

### 🔹 AI Supply Chain Security
- Model file security: why AI model files are code, not just data
- **Pickle exploitation** — PyTorch `.pt` and `.pkl` files use Python's pickle 
  serialisation, which executes arbitrary code on load. 
  A malicious model downloaded from an untrusted source achieves RCE 
  when loaded in any Python environment
- **ONNX/SafeTensors** — Safer alternatives to pickle and why the ecosystem 
  is moving toward them, but migration is incomplete
- **Hugging Face supply chain** — Malicious models published on model repositories, 
  typosquatting of popular model names, backdoored fine-tunes
- Training data poisoning: injecting malicious examples into training datasets 
  to create backdoors or bias model behaviour in attacker-controlled ways
- **Backdoor attacks** — Models trained to behave normally except when triggered 
  by a specific input pattern — undetectable through normal evaluation

### 🔹 RAG Security (Retrieval-Augmented Generation)
- How RAG systems work: vector database retrieval → context injection → 
  LLM response generation
- **Indirect prompt injection via documents** — Injecting instructions into 
  documents that will be retrieved and processed by the AI, 
  causing it to execute attacker instructions as if they were legitimate context
- **Data poisoning in vector stores** — Inserting malicious documents into 
  the retrieval database to influence AI responses for all users
- **Embedding inversion attacks** — Reconstructing original text from 
  vector embeddings that were assumed to be irreversibly transformed
- **Context manipulation** — Crafting retrieved content that causes the 
  model to ignore legitimate context and substitute attacker-controlled information

### 🔹 AI-Assisted Offensive Security
- Using LLMs to accelerate penetration testing: automated reconnaissance, 
  payload generation, vulnerability research, and report writing
- AI for social engineering: generating convincing phishing content, 
  voice cloning for vishing, deepfake considerations
- AI-powered fuzzing and vulnerability discovery
- The dual-use nature of AI security tools: the same capabilities that 
  help defenders detect attacks help attackers craft them

### 🔹 Defensive AI
- AI/ML in SIEM and security operations: anomaly detection, 
  alert prioritisation, automated triage
- Using ML for network traffic analysis: identifying C2 beaconing, 
  lateral movement patterns, and data exfiltration
- Model monitoring in production: detecting prompt injection attempts, 
  output anomaly detection, input/output logging and auditing
- LLM guardrails and their limitations: why filter-based defences are 
  inherently gameable and what more robust architectures look like
- Responsible AI deployment checklist: input validation, output sanitisation, 
  privilege separation, least-privilege tool access for AI agents

---

## Tools & Technologies Encountered
`Burp Suite` (for intercepting LLM API traffic) `Python` `LangChain` 
`Hugging Face` `OWASP LLM Top 10` `Garak` (LLM vulnerability scanner)

---

## Key Takeaways (So Far)

Prompt injection is the new SQL injection — and it is harder to fix because 
there is no equivalent of a parameterised query for natural language. 
Every organisation deploying LLMs in any capacity that handles sensitive data 
or has access to internal systems has an AI attack surface they likely 
have not assessed. The supply chain angle is particularly alarming: a model 
downloaded from a public repository can achieve arbitrary code execution the 
moment it is loaded, with no indication to the developer that anything is wrong. 
AI security is not a niche specialisation — it is becoming a core competency 
for any penetration tester working in modern environments.
