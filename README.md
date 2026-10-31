# PortSwigger Notes — Web Security Academy Write-ups

> A structured knowledge base of my hands-on practice on the [PortSwigger Web Security Academy](https://portswigger.net/web-security), documenting exploitation methodology, request/response analysis, and root-cause reasoning across modern web vulnerability classes.

![Status](https://img.shields.io/badge/status-in%20progress-blue)
![Platform](https://img.shields.io/badge/platform-PortSwigger%20Academy-orange)
![Focus](https://img.shields.io/badge/focus-web%20security-purple)
![License](https://img.shields.io/badge/license-MIT-green)

---

## 📖 About

This repository documents my journey through the **PortSwigger Web Security Academy** — widely regarded as the most comprehensive free resource for learning web application security. Each write-up captures not only the payload that solved the lab, but the **reasoning trail**: what I observed, what hypothesis I formed, how I tested it, and why it worked.

The goal is to build a **deep, transferable understanding** of web vulnerabilities — the kind that applies to real pentest engagements and bug bounty work, not just to lab completion.

**What I'm building here**
- A consistent methodology for approaching unfamiliar vulnerabilities
- Fluency in reading and manipulating HTTP requests via Burp Suite
- A reference library I can return to during engagements and interviews
- A documented portfolio of hands-on practice

---

## 🛠️ Tools & Environment

| Tool | Purpose |
|---|---|
| **Burp Suite (Community)** | Intercepting, replaying, and manipulating HTTP requests |
| **Browser DevTools** | Inspecting DOM, cookies, and client-side behavior |
| **curl** | Quick request crafting outside the proxy |
| **PortSwigger Web Security Academy** | Isolated lab environment for each vulnerability class |

All labs are performed within PortSwigger's hosted environment — no external systems are targeted.

---

## 🎯 Methodology

Every write-up follows a consistent structure:

1. **Lab overview** — what the lab simulates and what the objective is.
2. **Reconnaissance** — what I observed (endpoints, parameters, headers, responses).
3. **Hypothesis** — what vulnerability class seems likely, and why.
4. **Exploitation** — the payload, request, or technique used, step by step.
5. **Result** — confirmation that the objective was achieved.
6. **Root cause** — why the application is vulnerable at a design or code level.
7. **Takeaway** — what I would look for next time, and how it could be prevented.

This structure forces me to think like a pentester rather than a lab-solver.

---

## 🗂️ Repository Structure

```text
portswigger-notes/
├── PORTSWIGGER/                        # Write-ups organized by vulnerability class
│   ├── API testing/
│   ├── Access control vulnerabilities/
│   ├── CSRF/
│   ├── Clickjacking/
│   ├── File upload vulnerabilities/
│   ├── Insecure Deserialisation/
│   ├── NoSQLi/
│   ├── OS Command injection/
│   ├── Path traversal/
│   ├── Prototype pollution/
│   ├── Race conditions/
│   ├── SQLi/
│   ├── SSRF/
│   ├── Web Cache Deception/
│   ├── XSS/
│   └── XXE/
├── images/                             # Screenshots used across write-ups
└── README.md
```

Each folder contains one or more markdown write-ups for the labs completed in that category.

---

## 📚 Vulnerability Coverage

| Category | Class | Write-ups |
|---|---|---|
| **Server-side** | SQL Injection | ✅ |
| | OS Command Injection | ✅ |
| | Path Traversal | ✅ |
| | File Upload | ✅ |
| | SSRF | ✅ |
| | XXE | ✅ |
| | Insecure Deserialisation | ✅ |
| | NoSQL Injection | ✅ |
| | API Testing | ✅ |
| | Access Control | ✅ |
| **Client-side** | XSS (Reflected / Stored / DOM) | ✅ |
| | CSRF | ✅ |
| | Prototype Pollution | ✅ |
| | Web Cache Deception | ✅ |
| **Advanced** | Race Conditions | ✅ |


---

## ⭐ Featured Write-ups

A few notes that best represent my methodology and depth of analysis:

- **[API Testing](./PORTSWIGGER/API%20testing/)** — three labs covering endpoint discovery via documentation, unused HTTP methods (`PATCH`), and mass assignment through JSON parameter tampering.
- **[SQL Injection](./PORTSWIGGER/SQLi/)** — from error-based detection to `UNION`-based extraction, with step-by-step reasoning across error responses.
- **[XSS](./PORTSWIGGER/XSS/)** — reflected, stored, and DOM-based contexts, including sink analysis.
- **[Access Control](./PORTSWIGGER/Access%20control%20vulnerabilities/)** — IDOR, privilege escalation, and broken horizontal/vertical authorization.

> *(Update links if folder names change.)*

---

## 🔍 What I Look For in Every Lab

Rather than memorizing payloads, I train myself to notice the signals that reveal a vulnerability class:

- **Unusual HTTP methods** in `OPTIONS` or error responses (e.g. `PATCH`, `DELETE`).
- **Reflected input** in responses — candidate for reflected XSS or SQLi.
- **Parameters not expected by the UI** — candidate for mass assignment or hidden fields.
- **Differences in response length, status, or timing** — candidate for blind injection or race conditions.
- **Client-side JavaScript handling user input** — candidate for DOM XSS or prototype pollution.
- **URL parameters pointing to resources** (e.g. `?url=`, `?file=`) — candidate for SSRF or path traversal.

This checklist has become my default mental model when approaching any new target.

---

## ⚖️ Disclaimer

All labs are performed within **PortSwigger Web Security Academy's isolated, legal lab environment**. Techniques documented here are for **educational purposes only** and must not be used against systems without explicit written authorization.

---

## 📬 Contact

- **GitHub:** [@ngngocatr](https://github.com/ngngocatr)

---

## 📄 License

Released under the [MIT License](./LICENSE).