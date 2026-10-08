[Contents](../README.md) · [Previous](01-technical-foundations.md) · [Next](03-account-mapping.md)

# 2. Fundamentals of UPI Identifiers & Prefix Derivation
Here, we can broadly categorize UPI ID derivation into three main formats: **email-based identifiers, phone-number-based identifiers, and custom identifiers**.
- **Email-based:** Primarily used by Google Pay (GPay), where the UPI ID may be derived from the user's email-related identifier.
- **Phone-number-based:** A common format used by several UPI applications, where the user's registered mobile number is incorporated into the UPI ID.
- **Custom identifiers:** Some UPI applications generate or allow identifiers based on custom usernames or other naming conventions. In certain cases, these custom formats may also be used as the default UPI ID.
The exact derivation method depends on the UPI application, account configuration, and the user's selected identifier.
### **2.1 Email-Driven VPA Derivation:**
During Google Pay account creation and usage, the application may derive the user's Virtual Payment Address (VPA) identifier from information associated with their Google Account. 
In particular, the Gmail username/prefix can be used as part of the VPA generation process, allowing the resulting payment identifier to be automatically created based on the user's existing Google account identity rather than requiring the user to manually choose a separate identifier.
![Figure 3](../assets/figure-03.png)
This is how **Google Pay Derive UPI** Identifire from **Email ID**
If a UPI ID is deterministically derived from a Gmail username, the relationship may work in both directions: a UPI ID can potentially be used to infer the associated email address, while the email address may be used to derive or predict the UPI ID. This depends on the specific VPA-generation mechanism.
![Figure 4](../assets/figure-04.png)
There may be exceptions where a user’s Gmail username does not correspond to the identifier used in their email address or other online services. However, in many cases, users tend to use the same or a closely related username across different services.
**Common examples:**
- Gmail: `bob123@gmail.com` → Username: `bob123`
- Gmail: `john.smith@gmail.com` → Username: `john.smith`
- Gmail: `alex1998@gmail.com` → Username: `alex1998`
**Examples of exceptions:**
- Gmail: `bob123@gmail.com` → Other identifier: `robert456`
- Gmail: `john.smith@gmail.com` → Other identifier: `js789`
- Gmail: `alex1998@gmail.com` → Other identifier: `alexander01`
Therefore, while this correlation may not work for every user, cases where the same or a closely related username is reused across services can potentially allow an exposed identifier to be correlated with an associated email address.
### 2.2 Phone-Number-Driven VPA Derivation
This is common mapping mainly happens but from overall apps In here we take **PhonePe** as example.
A VPA handle formatted as `9876543210@ybl` directly leaks the user's mobile phone number `9876543210`. <br>
![Figure 5](../assets/figure-05.png)
If a UPI ID is deterministically derived from a user's phone number, the relationship may work in both directions: the UPI ID can potentially reveal or help infer the associated phone number, while the phone number may be used to derive or predict the corresponding UPI ID. This depends on the specific UPI app and VPA-generation mechanism.<br>
![Figure 6](../assets/figure-06.png)
- Using the identified method, a phone number can be associated with a specific, verified bank account.
### 2.3 Custom Identifier Driven VPA Derivation 
Some UPI applications allow users to create VPAs using custom handles, and in certain cases, these identifiers may be assigned as the default VPA. Because custom VPAs are not necessarily generated from a predictable data source, directly reversing or deriving the underlying account information can be difficult.
However, custom identifiers may still contain patterns based on common human behavior. Since UPI IDs are frequently used as public-facing payment identifiers, users may choose names, numbers, or combinations that are familiar or meaningful to them. For example:
- **Name-based:** `bob123@upi`, `bob.light@upi` : Uses a name, nickname, or personal identifier.
- **Date-based:** `bob1998@upi` : `1998` could represent a birth year in **YYYY** format.
- **Phone-related:** `bob9876@upi` : `9876` could represent the last four digits of a phone number.
- **Vehicle-related:** `bobcar21@upi` : `21` could represent a vehicle-related number, such as a registration or model identifier.
- **Occupation/hobby-based:** `bobcoder@upi` : `coder` may represent the user's profession, skill, or hobby.
- **Frequently reused identifier:** `bob4521@upi` : `4521` could be a personally meaningful or frequently reused number across different services.
Similar patterns are commonly observed when users create email addresses and other online usernames. Therefore, although a custom VPA may not be directly reversible, correlations may sometimes be established by identifying reused usernames, meaningful numbers, and other publicly observable patterns. When multiple independent data points align, these behavioral patterns can potentially help connect otherwise separate identifiers.
### 2.4 App & Bank Fingerprinting via PSP Handles
![Figure 7](../assets/figure-07.png)
#### **TPAP Handles (****`@okicici`****, ****`@ybl`****, etc.)**
• **Indication of App Association, Not the User's Bank**<br>    ◦ Suffixes such as `@okicici` or `@ybl` primarily reveal the front-end application and the partnering Payment Service Provider (PSP) bank handling the infrastructure.   <br>    ◦ For instance, `@okicici` indicates that Google Pay utilizes ICICI Bank as its back-end PSP for transaction routing, while `@ybl` typically points to Yes Bank.<br>• **Insights into User Preference**<br>    ◦ These handles indicate which ecosystem or third-party application (such as Google Pay, PhonePe, or Amazon Pay) the user prefers for initiating transactions.   <br>    ◦ They provide app-based behavioral insights rather than exposing the user’s primary banking institution, as a user can link virtually any bank account to these applications regardless of the handle's suffix.
![Figure 8](../assets/figure-08.png)
#### **Direct Bank Handles (****`@kotak`****, ****`@icici`****, ****`@axis`****, ****`@sbi`****)**
- **Direct Association with the Bank's Ecosystem**
	- Handles matching a bank's native identifier indicate that the user is interacting directly through that specific financial institution's proprietary UPI application (e.g., YONO SBI, iMobile ICICI, or Kotak Mobile Banking).
	Example: 
	<table>
<tr>
<td>**Bank Name**</td>
<td>**UPI Handle Suffix**</td>
<td>**Native Bank UPI Application**</td>
</tr>
<tr>
<td>**State Bank of India (SBI)**</td>
<td>`@sbi`</td>
<td>YONO SBI / BHIM SBI Pay</td>
</tr>
<tr>
<td>**ICICI Bank**</td>
<td>`@icici`</td>
<td>iMobile Pay</td>
</tr>
<tr>
<td>**Kotak Mahindra Bank**</td>
<td>`@kotak`</td>
<td>Kotak Mobile Banking App</td>
</tr>
<tr>
<td>**Axis Bank**</td>
<td>`@axis`</td>
<td>Axis Mobile</td>
</tr>
<tr>
<td>**HDFC Bank**</td>
<td>`@hdfc`</td>
<td>HDFC NetBanking / PayZapp</td>
</tr>
	</table>
- **Direct Proof of a Banking Relationship or Service**
	- Unlike third-party handles, a direct bank VPA strongly implies an existing relationship with that institution.
	- The user either maintains an active bank account with them or utilizes a specific financial service/product offered by that exact bank, as native apps require direct verification through the bank's internal core systems without relying on an intermediate third-party PSP.
### 2.5 Specific Scenarios: 
### **Scenario 1: Identifying UPI Application and Banking Signals from an Observed Handle**
If a user's mobile number is already known and a UPI identifier associated with that number is observed, the UPI handle or suffix provides valuable behavioral and infrastructural insights. However, the nature of these insights depends entirely on whether the handle belongs to a **Third-Party Application Provider (TPAP)** or a **Direct Bank Native Application**.
#### **1. TPAP-Facilitated Handles (App-Level Signals)**
- An identifier ending in a TPAP suffix points to the front-end application and its partner Payment Service Provider (PSP) routing arrangement rather than the user's home bank.
- **Examples of TPAP Signatures:**
	- `@okicici` / `@okaxis` / `@okhdfcbank` / `@oksbi` → Google Pay ecosystem and its corresponding partner PSP routing.
	- `@ybl` → PhonePe ecosystem and YES Bank PSP routing.
- **Interpretation:** These should be treated as application-level attribution signals indicating that the user interacts with that specific fintech ecosystem. Google Pay and other TPAPs explicitly state that a customer can link an account from any bank (e.g., an SBI account) regardless of whether the suffix is `@okicici` or `@okaxis`. NPCI’s architecture cleanly separates the PSP role from the customer’s actual remitter bank account.
#### **2. Direct Bank Native Handles (Account/Service-Level Signals)**
- Handles that match a bank's distinct identifier indicate that the user is transacting directly through that financial institution's proprietary mobile banking application.
- **Examples of Direct Bank Signatures:**
	- `@sbi` → SBI native application (e.g., YONO SBI).
	- `@icici` → ICICI Bank native application (e.g., iMobile Pay).
	- `@kotak` → Kotak Mahindra Bank native application.
	- `@axis` → Axis Bank native application.
- **Interpretation:** Unlike TPAP handles, a direct bank VPA strongly implies an active banking relationship. Because native bank apps require internal core system verification without an intermediate third-party PSP, observing a direct bank handle suggests the user holds an active account or utilizes specific financial services with that exact institution.
### Scenario 2 : Application-Specific UPI Handle as a Service-Usage Indicator
If the observed UPI suffix is **application-specific**, it can provide additional context about the type of financial service or ecosystem with which the identifier is associated.
For example, **`@yescred`**** is listed by YES BANK as a UPI handle for ****CRED**. CRED is primarily positioned around credit-card management, credit-card bill payments, credit scoring, rewards, and related financial services, while also providing UPI functionality.
Therefore, an observed identifier such as:
**`username@yescred`**
can reasonably provide an **application-level signal that the UPI identifier is associated with CRED**.
From an analytical perspective, this may indicate that the user has an association with or has used CRED's services. Because CRED's services include credit-card management and bill-payment functionality, the observation can provide **context suggesting possible use of credit-related services**.
However, this should **not** be interpreted as proof that the person currently owns or uses a credit card. The UPI handle identifies the CRED/PSP relationship; it does not disclose the user's complete financial profile or the specific financial products they hold.
This makes application-specific handles potentially more informative than generic UPI identifiers when performing **high-level application/ecosystem attribution**.
### Scenario 3 : UPI Handle Enumeration
If we already know a user's UPI ID or phone number, different UPI handles can potentially be tested against the same identifier.
For example:
`number@ybl`
`number@yescred`
If a matching UPI ID exists, the handle can provide a signal about the associated UPI application or PSP ecosystem.
This approach works mainly when the application uses a predictable identifier such as a mobile number or recognizable VPA. It may fail when the user has a **custom VPA, randomized identifier, or different UPI ID structure**.

---

[Contents](../README.md) · [Previous](01-technical-foundations.md) · [Next](03-account-mapping.md)
