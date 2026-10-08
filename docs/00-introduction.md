[Contents](../README.md) · [Next](01-technical-foundations.md)

# Introduction & Executive Summary
**Author:** Nabeel, Security Researcher, nabeel@soobik.com
#### 1. Background & Context
The Unified Payments Interface (UPI) ecosystem, managed by the National Payments Corporation of India (NPCI), has evolved into one of the world's largest real-time payment networks. To ensure seamless transaction routing and foster trust between transacting parties, Third-Party Application Providers (TPAPs) such as Google Pay, Amazon Pay, PhonePe, and CRED frequently surface transaction metadata, beneficiary display names, and payment routing feedback to remitters.
#### 2. Problem Statement
While individual payment applications implement localized user interface (UI) obfuscation and data masking rules, the ecosystem lacks unified, server-side data sanitization standards. This research demonstrates how an adversary can exploit architectural inconsistencies across distinct TPAPs to execute targeted, non-invasive reconnaissance. By analyzing the fragmented metadata exposed during pre-transaction lookups, nominal payment executions, and side-channel error handling, a threat actor can systematically bypass local UI-level masking con
#### 3. Research Objectives & Methodology
This research presents a formal analysis of **"UPI Mosaic Reconnaissance" **a methodology leveraging pattern recognition, cross-application metadata correlation, and business logic abuse. The key objectives of this study are to:
- **Expose Metadata Aggregation Vectors**: Document how isolated parameters exposed across different TPAPs (e.g., avatar photos, ecosystem usernames, and partial VPA strings) can be synthesized to reverse-engineer full Virtual Payment Addresses (VPAs).
- **Demonstrate Financial Graphing Capabilities**: Detail how pre-fetch API mechanisms and default receiving account behaviors allow an investigator to map a target's primary bank accounts and linked RuPay credit instruments without executing unauthorized transfers.
- **Evaluate Payload & Error Side Channels**: Analyze direct data leaks occurring via raw QR code parameters and payment rejection error responses.
- **Propose Systemic & Regulatory Mitigation**: Highlight compliance gaps within current mandates (including NPCI Circular `NPCI/UPI/OC-234/2026-27`) and outline architectural defense strategies for developers and policy architects.
All observations and data points referenced throughout this report were gathered exclusively through passive observational methods, self-controlled accounts, and authorized security testing workflows.

---

[Contents](../README.md) · [Next](01-technical-foundations.md)
