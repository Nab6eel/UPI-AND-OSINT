[Contents](../README.md) · [Previous](00-introduction.md) · [Next](02-upi-identifiers.md)

# 1. Technical Foundations: Key Terminology & Architecture
- **UPI (Unified Payments Interface):** <br>The real-time payment protocol managed by NPCI that replaces traditional bank account details with simple digital aliases.

<img src="../assets/figure-01.png" alt="Figure 1" width="720">

- **VPA (Virtual Payment Address):**<br>The primary identifier/alias used for routing payments (e.g., `username@handle`). This serves as the main **OSINT Pivot Point**.

<img src="../assets/figure-02.png" alt="Figure 2" width="722">

- **PSP (Payment Service Provider) & TPAP (Third-Party Application Provider): <br>TPAP:** Front-end payment applications (e.g., Google Pay, PhonePe, Paytm, Amazon Pay). <br>**PSP:** The partner banking infrastructure powering the TPAP (e.g., ICICI, Axis, SBI, Yes Bank).   
- **NPCI UPI Mapper:** The central lookup database that routes money sent via a phone number to a user's chosen primary payment app.

---

[Contents](../README.md) · [Previous](00-introduction.md) · [Next](02-upi-identifiers.md)
