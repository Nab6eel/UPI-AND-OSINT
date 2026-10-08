[Contents](../README.md) · [Previous](04-vpa-patterns.md) · [Next](06-remediation-and-ethics.md)

# 5. Pre & Post Bank account or service validation from UPI VPA

### 5.1 Pre-Fetching Bank Details From UPI VPA
**Pre-Transaction Financial Leakage**: Certain TPAP applications (such as Amazon Pay and CRED) implement a feature that pre-fetches and displays the beneficiary's connected bank or financial institution before a transaction is authorized. While designed to reassure the remitter that funds are going to the correct destination, this mechanism acts as an unintended data leak. It allows anyone to identify where a target holds active accounts without ever executing a payment. For example, querying the VPA `bob123@okaxis` in Amazon Pay explicitly reveals a connection to **Kotak Mahindra Bank**.   

	

<img src="../assets/figure-19.png" alt="Figure 19" width="500">

	
	

<img src="../assets/figure-20.png" alt="Figure 20" width="496">

	

**Credit Card Exposure**: This pre-fetch behavior is not limited to standard savings accounts. As the ecosystem has expanded to support credit on UPI, these interfaces will also resolve and display connected credit instruments. Querying a secondary handle like `bob123-2@okaxis` can expose that the user has linked a **Canara Bank RuPay Credit Card**.   
**Financial Graphing via Enumeration**: When combined with VPA suffix enumeration (e.g., iterating through `bob123`, `bob123-1`, `bob123-2`), this pre-fetch mechanism becomes a powerful mapping tool. By methodically resolving permutations of a target's handle, an investigator can construct a comprehensive "financial graph," mapping out multiple banking relationships and credit holdings tied to a single identity.   
**Ecosystem Limitations & Cross-Verification**: This reconnaissance method has known technical constraints:
- **Synchronization Delays**: The pre-fetched bank data may occasionally be outdated due to ecosystem caching. If a user recently changed their default receiving account, it takes time for the central switch to propagate this to all TPAPs.
- **Resolution Failures**: Applications sometimes struggle to accurately resolve routing data for smaller regional banks or niche financial services, either returning blank fields or generic errors.
- **Secondary Validation**: To counter these inaccuracies, we can frequently employ cross-verification by taking the target VPA and querying it across a secondary application to validate if the underlying bank routing data matches the initial discovery.

### 5.2 Post & Receive payment Fetching Bank Details From UPI VPA

#### 5.2.1. Post-Transaction Metadata Exposure (Nominal Payment Verification)
**Receipt-Based Financial Discovery**: Beyond pre-fetch lookups, executing a nominal transaction (e.g., ₹1) or processing an incoming transfer generates a confirmed transaction log. Certain TPAPs (such as Google Pay) expose extended metadata within the transaction receipt that was unverified prior to payment.

<img src="../assets/figure-21.png" alt="Figure 21" width="316">

**Originating Bank Identification**: As shown in transaction receipts, the interface explicitly appends the originating or receiving financial institution to the entity's name (e.g., `From: Alice Boby (State Bank of India)`). This provides definitive post-transaction confirmation of the target's underlying bank account, validating data gathered during initial pre-fetch or permutation stages

#### 5.2.3 Error-Handling Side-Channel Analysis (Payment Instrument Distinction)
**Rejection Message Leakage**: When initiating a payment, difference in routing logic between standard savings accounts and credit-linked VPAs (such as RuPay Credit Cards on UPI) creates distinct error-handling behaviors.
**Merchant & Bank Constraints**: If a transaction is attempted using a credit-backed instrument against a VPA or merchant that does not support credit-on-UPI transactions, the central switch or gateway returns an explicit failure response (e.g., *"Payment failed as this merchant doesn't accept payment from a RuPay card. Retry using a Bank account?"*).

	

<img src="../assets/figure-22.jpg" alt="Figure 22" width="760">

	
	
The behavior was analyzed while attempting a direct RuPay credit-card payment through a standard savings-account UPI flow.
	

**Instrument Fingerprinting**: An investigator can use these explicit error responses as an information-disclosure side channel. By intentionally testing payment attempts across different instrument types, the response behavior confirms the operational constraints and account capabilities tied to the target VPA. 

### 5.3 Default Receiving Account Discovery via Multi-TPAP VPA Resolution
- **Multi-App Account Mapping**: Users frequently install multiple Third-Party Application Providers (TPAPs) such as Google Pay, Amazon Pay, PhonePe, and Paytm and set different primary receiving accounts across each platform (e.g., setting Kotak Mahindra Bank as the default on Google Pay, Axis Bank on Amazon Pay, and ICICI Bank on PhonePe).
- **Application-Specific VPA Routing**: Because distinct TPAPs generate platform-specific VPA handles (e.g., `user@okaxis` for Google Pay, `user@apl` for Amazon Pay, `mobile@ybl` for PhonePe), querying each app-specific handle via pre-fetch resolution mechanisms exposes the specific default receiving bank account bound to that particular app setup.
- **Aggregated Multi-Bank Fingerprinting**: By combining a target's identified username/prefix with various TPAP-specific handles (`@okaxis`, `@oksbi`, `@apl`, `@ybl`, `@paytm`), an we can execute cross-application lookups. Resolving these distinct VPAs systematically uncovers the complete list of financial institutions where the target maintains active accounts.
- **Exploitation of Behavioral Preferences**: This vector leverages standard user configuration habits such as assigning different default accounts for personal spending, business transactions, or secondary funds across separate apps allowing an analyst to reconstruct the target's entire multi-bank profile without ever executing a transaction.

### 5.4 Airtel Upi and Bank Account
During Analysis, I observed that Airtel UPI identifiers commonly follow a format based on the user's registered mobile number, such as `91987654321@airtel`. Based on the enumeration observations described above, the identified UPI identifiers appeared to correlate with the corresponding user records and bank account information within the system.
In particular, the system appears to reuse or derive the bank account identifier from the user's registered mobile number. Consequently, where a valid UPI identifier or phone number is available, it may be possible to infer or enumerate the corresponding bank account number associated with the user.
This creates a potential information-disclosure and account-enumeration concern, as knowledge of a user's UPI identifier or registered phone number could potentially expose sensitive financial account information.

---

[Contents](../README.md) · [Previous](04-vpa-patterns.md) · [Next](06-remediation-and-ethics.md)
