# How We Generate OTP (One-Time Password)

## What is an OTP?

OTP, or One-Time Password, is a unique code that is valid for only one login session or transaction. We commonly use this mechanism to provide an additional layer of security, especially for online services and banking.

## Why Do We Use OTP?

By using OTPs, we increase our account security by making unauthorized access more difficult, even if our password is compromised. Each OTP is valid for only a short period or a single use, which reduces the risk of misuse.

## What are DEK and KEK?

In secure OTP generation systems, we often encounter the terms DEK and KEK:

### Data Encryption Key (DEK)
The DEK is a key we use to encrypt and decrypt sensitive data, such as secrets or passwords. In the context of OTP, the DEK protects the secret we use to generate the OTP, making it more difficult for attackers to access or misuse the secret.

**Example:**
- When we store a user's secret in a database, we can encrypt it with the DEK. Only systems with access to the DEK are able to decrypt and use the secret for OTP generation.

### Key Encryption Key (KEK)
The KEK is a key we use to encrypt and protect other keys, such as the DEK. This adds another layer of security. Even if the DEK is compromised, it cannot be used unless the KEK is also available.

**Example:**
- We encrypt the DEK with the KEK before storage. To use the DEK, our system must first decrypt it using the KEK.

**In summary:**
- **KEK** protects the **DEK**.
- **DEK** protects the secret (and other sensitive data).
- We use the secret to generate the OTP.


## How is an OTP Generated?


There are several common methods we can use to generate OTPs:

### 1. Random Number Generation
The simplest way is to generate a random number, usually 4-8 digits. For example, a 6-digit OTP might look like `482193`.

**How it works:**
- Our server generates a random number when we request an OTP.
- The OTP is sent to us (via SMS, email, or app).
- We enter the OTP to verify our identity.

### 2. Time-based OTP (TOTP)
This method uses the current time as part of the code generation. The OTP changes every 30 or 60 seconds.

**How it works:**
- Both our device and the server share a secret key.
- The current time is combined with the secret key to generate the OTP.
- We can use applications such as Google Authenticator that use this method.

### 3. HMAC-based OTP (HOTP)
This method uses a counter that increases every time we request a new OTP.

**How it works:**
- Both our device and the server share a secret key.
- The counter value is combined with the secret key to generate the OTP.
- The OTP remains the same until we request a new one.

## Example: OTP Generation Using Secret and DEK (JavaScript)

In a more secure OTP system, we use a secret and a Data Encryption Key (DEK) to generate the OTP. This approach is similar to how TOTP (Time-based One-Time Password) works, but the following is a simplified example using HMAC and a secret key.

```javascript
const crypto = require('crypto');

function generateOTP(secret, dek, length = 6, intervalInSecond = 30) {
	// Combine secret and DEK
	const key = crypto.createHmac('sha256', dek).update(secret).digest();
	// Use current time as a moving factor, interval in seconds
	const time = Math.floor(Date.now() / (intervalInSecond * 1000));
	// Create HMAC with key and time
	const hmac = crypto.createHmac('sha256', key).update(time.toString()).digest('hex');
	// Take digits from the HMAC output
	let otp = '';
	for (let i = 0; otp.length < length && i < hmac.length; i++) {
		if (!isNaN(hmac[i]) && otp.length < length) {
			otp += hmac[i];
		}
	}
	// If not enough digits, pad with zeros
	return otp.padEnd(length, '0');
}

// Example usage:
const secret = 'user-specific-secret';
const dek = 'encryption-key';
const interval = 60; // OTP changes every 60 seconds
console.log(generateOTP(secret, dek, 6, interval)); // e.g., '482193'
```

This example uses a secret and a DEK to generate a secure OTP that changes at a specified interval. In real-world applications, we recommend using well-tested libraries for OTP generation and key management.

## Security Tips

- We should never share our OTPs with others.
- We should only enter OTPs on trusted websites or applications.
- If we receive an OTP without requesting it, we should contact the service provider immediately.

## Summary

OTPs are a simple but effective way for us to secure our accounts. We can generate OTPs using random numbers, time-based algorithms, or counters. We should always keep our OTPs private and use them only as intended.

## References

- [RFC 4226: HOTP: An HMAC-Based One-Time Password Algorithm](https://datatracker.ietf.org/doc/html/rfc4226)
- [RFC 6238: TOTP: Time-Based One-Time Password Algorithm](https://datatracker.ietf.org/doc/html/rfc6238)
- [OWASP: Authentication Cheat Sheet - One-Time Passwords](https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html#one-time-passwords)
- [Wikipedia: One-time password](https://en.wikipedia.org/wiki/One-time_password)
