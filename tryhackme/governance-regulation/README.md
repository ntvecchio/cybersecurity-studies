# 🏛️ Governance & Regulation

**Platform:** TryHackMe  
**Category:** Blue Team / GRC / Compliance  
**Date:** July 19, 2026

---

## Overview

Information security is not only about technical controls — it also requires a structured
governance and compliance framework. This room covers the foundations of how organizations
manage security through policies, regulations, and frameworks like GRC, GDPR, NIST, ISO 27001, and SOC 2.

---

## 📌 Key Terminologies

| Term | Definition |
|------|-----------|
| **Governance** | Managing and directing an organization to achieve its objectives and ensure compliance with laws and standards |
| **Regulation** | A rule or law enforced by a governing body to ensure compliance and protect against harm |
| **Compliance** | The state of adhering to laws, regulations, and standards that apply to an organization |

---

## 🏢 Information Security Governance

A structure of policies, methods, and guidelines designed to guarantee the **confidentiality,
integrity, and availability** of information assets. Managed by top-tier management and includes:

- **Strategy:** Developing a comprehensive security strategy aligned with business objectives
- **Policies and procedures:** Governing the use and protection of information assets
- **Risk management:** Identifying threats and implementing mitigation measures
- **Performance measurement:** Establishing KPIs to measure program effectiveness
- **Compliance:** Ensuring adherence to relevant regulations

---

## 📋 Information Security Frameworks

| Component | Description |
|-----------|-------------|
| **Policy** | Formal statement outlining an organization's goals and principles |
| **Standard** | Document establishing specific requirements for a process or product |
| **Guideline** | Non-mandatory recommendations and best practices |
| **Procedure** | Set of specific steps for undertaking a particular task or process |
| **Baseline** | Minimum security standards an organization or system must meet |

### Developing Governance Documents — Steps

1. **Identify scope and purpose** — what the document covers and why
2. **Research and review** — relevant laws, regulations, industry standards
3. **Draft the document** — clear, concise, actionable, aligned with goals
4. **Review and approval** — stakeholders, legal/compliance teams, management
5. **Implementation and communication** — train employees, assign responsibilities
6. **Review and update** — monitor compliance, adjust based on feedback and threat landscape changes

---

## ⚖️ GRC Framework (Governance, Risk, Compliance)

A holistic approach that integrates governance, risk management, and compliance.

| Component | Description |
|-----------|-------------|
| **Governance** | Sets direction through strategy, policies, standards, and monitoring |
| **Risk Management** | Identifies, assesses, prioritizes risks and implements controls |
| **Compliance** | Ensures legal, regulatory, and industry obligations are met |

### Developing a GRC Program — Steps

1. Define scope and objectives
2. Conduct a risk assessment
3. Develop policies and procedures
4. Establish governance processes
5. Implement controls (firewalls, IDS, IPS, SIEM, training)
6. Monitor and measure performance
7. Continuously improve

### GRC in the Financial Sector — Example

| Activity | Examples |
|----------|---------|
| **Governance** | Anti-money laundering policy, financial reporting, crisis management |
| **Risk Management** | Financial fraud risks, stolen credentials, fake ATM cards |
| **Compliance** | PCI-DSS, GLBA, SSL/TLS, patch management, phishing awareness |

---

## 🌍 Key Laws and Regulations

| Law/Regulation | Domain | Description |
|---------------|--------|-------------|
| **GDPR** | Data Privacy | EU regulation for protecting personal data of EU citizens |
| **HIPAA** | Healthcare | US law to protect health-related information |
| **PCI-DSS** | Financial | Secure handling of cardholder data (CHD) |
| **GLBA** | Financial | Protects nonpublic personal information (NPI) of financial customers |

### GDPR — Key Details

- Applies to any organization that processes EU residents' personal data
- Requires **consent** before collecting data
- Data must be **minimal** and **adequately protected**

**Penalty tiers:**

| Tier | Violations | Maximum Fine |
|------|-----------|-------------|
| **Tier 1** | Unintended data collection, sharing without consent | 4% of annual revenue OR €20 million (whichever is higher) |
| **Tier 2** | Breach notifications, cyber policies | 2% of annual revenue OR €10 million (whichever is higher) |

### PCI-DSS — Key Details
- Established by Visa, MasterCard, and American Express
- **CHD** = Cardholder Data
- Requires strict access controls, WAFs, encryption, and monitoring

---

## 🇺🇸 NIST 800-53

A publication by the National Institute of Standards and Technology providing a catalogue
of security controls organized into **20 control families**.

| Control | Category |
|---------|---------|
| Media Protection | Physical |
| Incident Response | Administrative |

### Compliance Best Practices — Phases
1. **Discover** — identify and catalogue data assets and threats
2. **Map** — correlate identified assets and controls to 800-53 families
3. **Govern** — create governance structure and assign responsibilities
4. **Monitor** — evaluate compliance, conduct audits

### NIST 800-63B
Guidelines for **digital identity** — authenticating and verifying identities of individuals accessing digital services.
Covers passwords, biometrics, tokens, and secure credential management.

---

## 📊 ISO/IEC 27001

International standard for an **Information Security Management System (ISMS)**.
Developed by ISO and IEC.

### Core Components

| Component | Description |
|-----------|-------------|
| **Scope** | Boundaries of the ISMS — covered assets and processes |
| **Information security policy** | High-level document defining the security approach |
| **Risk assessment** | Identifying and evaluating risks to CIA triad |
| **Risk treatment** | Selecting and implementing controls to reduce identified risks |
| **Statement of Applicability (SoA)** | Specifies which controls are applicable |
| **Internal audit** | Periodic audits to ensure ISMS is operating effectively |
| **Management review** | Regular review of ISMS performance |

---

## 🔍 SOC 2 (Service Organization Control 2)

Developed by **AICPA** — assesses the effectiveness of a company's data security controls.
Based on the **CIA triad** plus **Privacy**.

### When is it used?
Service providers that interact with client data — cloud companies, SaaS providers, etc.

### Planning a SOC 2 Audit — Steps
1. Determine the scope
2. Choose a qualified auditor
3. Plan the audit timeline and criteria
4. Prepare — review controls, identify gaps
5. Conduct the audit — interviews, documentation reviews, control tests
6. Receive the audit report with findings and recommendations

### SOC 2 Generic Controls

| Control | What it checks |
|---------|---------------|
| **Security** | Protection against unauthorized access |
| **Availability** | System remains available as agreed |
| **Confidentiality** | Data is protected from unauthorized disclosure |
| **Processing Integrity** | Processing is complete, valid, and timely |
| **Privacy** | Personal information is handled appropriately |

---

## 🧠 Key Concepts Summary

| Concept | Definition |
|---------|-----------|
| **Regulation** | Rule enforced by a governing body |
| **GRC** | Governance, Risk, Compliance — integrated framework |
| **GDPR** | EU data protection law — max fine 4% revenue (Tier 1) |
| **HIPAA** | US healthcare data protection |
| **PCI-DSS** | Payment card data security — CHD = Cardholder Data |
| **NIST 800-53** | US security controls catalogue — 20 control families |
| **ISO 27001** | International ISMS standard |
| **SOC 2** | Auditing framework for service organizations (AICPA) |
| **Risk treatment** | ISO 27001 component for implementing controls to reduce risks |
| **Availability** | SOC 2 control showing the system remains available |

---

## ✅ Room Answers

| Question | Answer |
|----------|--------|
| A rule enforced by a governing body is called? | Regulation |
| HIPAA targets which domain? | Healthcare |
| The step involving monitoring compliance and adjusting based on feedback? | Review and update |
| A set of specific steps for undertaking a task is called? | Procedures |
| GRC component for identifying and prioritizing risks? | Risk Management |
| Is it important to monitor and measure policy performance? | Yea |
| Maximum fine for GDPR Tier 1 (percentage)? | 4% |
| In PCI-DSS, CHD stands for? | Cardholder Data |
| NIST 800-53 — Media Protection control category? | Physical |
| NIST 800-53 — Incident Response control category? | Administrative |
| NIST phase that correlates identified assets and permissions? | Map |
| ISO 27001 component for implementing controls to reduce risks? | Risk treatment |
| SOC 2 control showing system remains available? | Availability |
