[Contents](../README.md) · [Previous](02-upi-identifiers.md) · [Next](04-vpa-patterns.md)

# 3. Multi-Account Mapping & Routing Mechanics
### 3.1 Multi-Account Suffix Enumeration
#### 3.1.1 UPI VPA Suffix Variation
Some UPI applications, such as **Google Pay**** **as example, may append a suffix such as **`-1`** to the main VPA identifier when the preferred identifier is already unavailable.
![Figure 9](../assets/figure-09.png)
Examples:
- `9876543210@upi` : `9876543210-1@upi`
- `bob123@upi` : `bob123-1@upi` 
This can occur when Application cannot claim the preferred VPA because that identifier is already registered or unavailable within the relevant UPI/PSP namespace.
Possible situations include:
1. **Changing UPI applications** : the previous application may still retain or have registered the original VPA.
2. **Multiple bank accounts or UPI registrations** : the same identifier may already be associated with another VPA.
3. **Multiple UPI IDs for the same account** : an alternative identifier may be generated when the preferred one is already occupied.
4. **Identifier collision** : another existing registration may already have claimed the preferred VPA.
Therefore, a suffix such as \*\*`-1` may indicate that the preferred VPA was unavailable and an alternative identifier was generated. If that identifier is also occupied, the application may continue with the next available variation, such as **`-2`****, ****`-3`****, ****`-4`****,** and so on. The exact generation and reuse rules depend on the application and PSP implementation.
### 3.2 Multi VPA (UPI VPA Suffix Enumeration)

	
		![Figure 10](../assets/figure-10.png)
	
	
		![Figure 11](../assets/figure-11.png)
	

	
		![Figure 12](../assets/figure-12.png)
	
	
		![Figure 13](../assets/figure-13.png)
	

If we already know one valid UPI ID belonging to a person, such as `bob123@okhdfcbank`, we can use the known username `bob123` as the starting point. We can then check for other possible UPI IDs by trying different suffixes, such as `bob123-1@okhdfcbank`, `bob123-2@okhdfcbank`, and `bob123-3@okhdfcbank`. We can also keep the same username and try other supported handles, such as `bob123@okaxis`, `bob123@okicici`, and `bob123@oksbi`.
For example, from the UPI IDs shown above, we could have `okbob123@okaxis` for Kotak, `okbob-1@okaxis` for Airtel Bank, `bob123-2@okaxis` for the Canara Bank RuPay Card, and `bob123-3@okaxis`. If one of these IDs is found to be valid, we can use the same approach to check for additional variations and handles.
Therefore,  we can use the known username and available handle patterns to identify other valid UPI IDs associated with the connected bank accounts or RuPay cards. The system could then enumerate the discovered UPI IDs and map each one to its respective connected account or RuPay card, provided the lookup is authorized and supported by the UPI system. This check is also usable for 9876543210@axis to 9876543210@ybl or 987654321-1@ybl enumeration too.
### 3.3 Single VPA (UPI VPA Suffix Enumeration)
- **Architecture**: Some Apps like WhatsApp UPI, Amazon pay bind a single primary VPA to the registered phone number (e.g., `@waicici`). Incoming payments route to whichever account is currently designated as the primary/default account, shifting dynamically without requiring new VPA strings.
- **Enumeration Constraints**:
	- **No Static Footprint**: Because only one primary VPA exists, we cannot harvest permanent auxiliary handles or numeric index suffixes (`1`, `2`).
	- **Point-in-Time State Visibility**: While dynamic architectures prevent a comprehensive multi-VPA sweep, we can still perform real-time checks to identify whichever bank is actively linked at that exact moment.
	- **User-Dependent Mapping**: Because observed shifts reflect manual user toggles rather than a static multi-account footprint, our visibility remains bound to the user's active app state at the time of the query.
We can only enumerate here point-in-time mapped account in this architecture base but still if it changed in time we still observe it.
### 3.4 Cross-Platform vs Same Platform UPI ID Exposure
####  Intra-Platform vs. Inter-Platform Masking Mechanics
- **Intra-Platform Transactions (e.g., Google Pay to Google Pay)**: Within the same app ecosystem, platforms often hide phone numbers and fully mask UPI identifiers for unknown or uncontacted senders (e.g., when there is no prior connection history) to protect user privacy. However, implementation varies widely some apps mask these details correctly, while others fail to obscure intra-platform data.
- **Inter-Platform Transactions (e.g., Paytm or other TPAPs paying a Google Pay user)**: Conversely, cross-platform routing often bypasses these front-end abstraction layers. As observed in inter-platform transfers, transaction logs frequently expose unmasked UPI identifier (e.g., displaying full handles such as `9876543210@ptypes`) and sender metadata directly without hiding the underlying phone number structure.
![Figure 14](../assets/figure-14.png)
Regardless of whether a transaction is intra-platform or inter-platform, many applications fail to apply masking correctly due to varied development logic, distinct handling of payload metadata. Consequently, raw, unmasked UPI IDs  are frequently exposed across various apps for no uniform reason other than poor implementation standards or mainly those platform thoughts for double verification of user with their upi id before starting a transcation can be reason these are shown to us.
Because of these inconsistent masking implementations across cross-platform or poorly secured intra-platform transactions, a tester or investigator can easily harvest the unmasked UPI ID (such as a mobile-number-based VPA) of an otherwise unknown or anonymous payer directly from the transaction log.
### 3.5 Real Name Resolution (UPI ID / Phone Number $`\rightarrow`$ Legal Name)
#### **3.5.1 Legitimate Verification Mechanism**: 
Real name resolution is a built-in feature of the UPI ecosystem designed for user verification. When a remitter enters a VPA or mobile number, the system queries the central switch to display the registered beneficiary's legal or display name, allowing remitters to confirm they are paying the correct party before authorizing a transfer.
![Figure 15](../assets/figure-15.png)
While intended as a security guardrail against misdirected funds and fraud, this feature is frequently leveraged for technical reconnaissance and OSINT validation. If an investigator or tester has obtained a target VPA or phone number through data leakage  passing that identifier into a UPI transfer prompt allows them to instantly resolve and verify the underlying legal identity tied to the account.
#### **3.5.2 Extra Verification from TPAPs:**
Beyond standard banking name resolution, certain TPAPs introduce an additional layer of personal data exposure. As observed in app lookup interfaces, querying an identifier can expose extended identity metadata such as the user's public Google profile picture, username, masked phone number, and account creation timeline (e.g., *Joined September 2021*) directly alongside the verified **"Banking name"**.

![Figure 16](../assets/figure-16.png)
While these visual elements (profile avatars and usernames) are built into the app's intended design to help users verify known contacts, they function as a privacy extension during technical reconnaissance. When an unknown identifier is queried, they act as an extra layer of personal metadata, bridging a transactional VPA directly to a broader public online profile.
This will work around with the applications that uses googles api for data will do the mostly same(eg: Cred), Cred have a option to user to connect user gmail id to cred application. This will show same or some what exact data to other cred users.
#### 3.5.3 TPAP Application and it’s Connected eco-system: 
Different digital ecosystems expose and organize user information in different ways, depending on their application architecture and available features. For example, Google Pay provides UPI-related information through its payment interface and google’s eco-system we all know we discussed it before, while WhatsApp offers a different perspective by combining messaging, profile visibility, and UPI payment functionality.
If a person's phone number is known, WhatsApp can potentially be used to determine whether that number is associated with an account, depending on the platform's behavior and privacy settings. If the user's profile picture and other profile details are publicly visible or accessible, these may provide additional identifying context. Furthermore, WhatsApp's built-in UPI functionality may provide a way to examine payment-related identifiers or account details that are exposed through the application's legitimate user interface.
Therefore, each digital ecosystem can serve as a distinct source of user-identification signals, with different data points, visibility rules, and interaction flows. Examining these differences across platforms can help establish how identifiers such as phone numbers, UPI IDs, profile information, and payment-related details are linked, while accounting for platform-specific privacy controls and data-access limitations.
### 3.6 UPI QR via VPA id
1. **Decoding raw URI scheme parameters from static/dynamic UPI QR code**s (`upi://pay?pa=...&pn=...`).   Extracting the unmasked Payee Name (`pn`) parameter directly from payment payloads. 
![Figure 17](../assets/figure-17.png)
upi://pay?pa=bob123@okaxis&pn=Boby%20Bob&cu=INR 
**Standardized Payload Transparency**: National Payments Corporation of India (NPCI) standards mandate the use of a uniform `upi://pay` URI scheme for generating static and dynamic payment QR codes. Because QR codes are plain-text string encodings, scanning a payment QR code directly exposes the underlying payment payload without requiring authorization or network interaction.  
**Direct Extraction of Unmasked Parameters**: While payment applications may apply UI-level masking (such as hiding parts of a phone number or VPA on screen), the underlying QR payload must retain unencrypted string parameters to allow any compliant UPI app to route the transaction. Key parameters extracted directly from the raw URI payload include:   <br>• `pa` (**Payee Address / VPA**): The exact, unmasked target payment handle (e.g., `bob123@okaxis`).   <br>• `pn` (**Payee Name**): The URL-encoded legal or display name of the recipient (e.g., `Boby%20Bob` -\> `Boby Bob`).   <br>• `cu` (**Currency**): The currency identifier (e.g., `INR`).

---

[Contents](../README.md) · [Previous](02-upi-identifiers.md) · [Next](04-vpa-patterns.md)
