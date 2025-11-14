# Bagaimana Kita Membuat OTP (One-Time Password)

## Apa itu OTP?

OTP, atau One-Time Password, adalah kode unik yang hanya berlaku untuk satu sesi login atau satu transaksi. Biasanya, kita menggunakan mekanisme ini untuk menambah lapisan keamanan, terutama pada layanan online dan perbankan.

## Mengapa Kita Menggunakan OTP?

Dengan menggunakan OTP, kita dapat meningkatkan keamanan akun karena akses tidak sah menjadi lebih sulit, bahkan jika kata sandi kita diketahui orang lain. Setiap OTP hanya berlaku dalam waktu singkat atau satu kali penggunaan, sehingga risiko penyalahgunaan menjadi lebih kecil.

## Apa itu DEK dan KEK?

Dalam sistem pembuatan OTP yang aman, kita sering menjumpai istilah DEK dan KEK:

### Data Encryption Key (DEK)
DEK adalah kunci yang kita gunakan untuk mengenkripsi dan mendekripsi data sensitif, seperti secret atau kata sandi. Dalam konteks OTP, DEK melindungi secret yang kita gunakan untuk menghasilkan OTP, sehingga lebih sulit bagi penyerang untuk mengakses atau menyalahgunakannya.

**Contoh:**
- Ketika kita menyimpan secret pengguna di database, secret tersebut dapat kita enkripsi dengan DEK. Hanya sistem yang memiliki akses ke DEK yang dapat mendekripsi dan menggunakan secret untuk pembuatan OTP.

### Key Encryption Key (KEK)
KEK adalah kunci yang kita gunakan untuk mengenkripsi dan melindungi kunci lain, seperti DEK. Ini menambah lapisan keamanan tambahan. Bahkan jika DEK berhasil diakses, kunci tersebut tidak dapat digunakan tanpa KEK.

**Contoh:**
- DEK kita enkripsi dengan KEK sebelum disimpan. Untuk menggunakan DEK, sistem harus mendekripsinya terlebih dahulu menggunakan KEK.

**Ringkasan:**
- **KEK** melindungi **DEK**.
- **DEK** melindungi secret (dan data sensitif lainnya).
- Secret kita gunakan untuk menghasilkan OTP.

## Bagaimana Kita Membuat OTP?

Ada beberapa metode umum yang bisa kita gunakan untuk membuat OTP:

### 1. Pembuatan Angka Acak
Cara paling sederhana adalah dengan membuat angka acak, biasanya 4-8 digit. Sebagai contoh, OTP 6 digit dapat berupa `482193`.

**Cara kerja:**
- Server akan menghasilkan angka acak saat kita meminta OTP.
- OTP dikirim ke kita (melalui SMS, email, atau aplikasi).
- OTP kita masukkan untuk verifikasi identitas.

### 2. OTP Berbasis Waktu (TOTP)
Metode ini menggunakan waktu saat ini sebagai bagian dari proses pembuatan kode. OTP akan berubah setiap 30 atau 60 detik.

**Cara kerja:**
- Perangkat dan server sama-sama memiliki secret key.
- Waktu saat ini digabungkan dengan secret key untuk menghasilkan OTP.
- Kita bisa menggunakan aplikasi seperti Google Authenticator yang memakai metode ini.

### 3. OTP Berbasis HMAC (HOTP)
Metode ini menggunakan penghitung (counter) yang bertambah setiap kali OTP baru diminta.

**Cara kerja:**
- Perangkat dan server sama-sama memiliki secret key.
- Nilai counter digabungkan dengan secret key untuk menghasilkan OTP.
- OTP akan tetap sama sampai kita meminta yang baru.

## Contoh: Pembuatan OTP Menggunakan Secret dan DEK (JavaScript)

Pada sistem OTP yang lebih aman, kita menggunakan secret dan Data Encryption Key (DEK) untuk menghasilkan OTP. Pendekatan ini mirip dengan TOTP (Time-based One-Time Password), namun berikut adalah contoh sederhana menggunakan HMAC dan secret key.

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

Contoh di atas menggunakan secret dan DEK untuk menghasilkan OTP yang aman dan berubah pada interval tertentu. Pada aplikasi nyata, sangat disarankan bagi kita untuk menggunakan library yang sudah teruji untuk pembuatan OTP dan manajemen kunci.

## Tips Keamanan

- Sebaiknya kita tidak pernah membagikan OTP kepada siapa pun.
- OTP hanya boleh kita masukkan pada situs atau aplikasi yang terpercaya.
- Jika kita menerima OTP tanpa pernah meminta, sebaiknya segera hubungi penyedia layanan.

## Ringkasan

OTP merupakan cara sederhana namun efektif untuk kita gunakan dalam mengamankan akun. OTP dapat kita hasilkan menggunakan angka acak, algoritma berbasis waktu, atau counter. OTP harus selalu kita jaga kerahasiaannya dan hanya digunakan sesuai kebutuhan.

## Referensi

- [RFC 4226: HOTP: An HMAC-Based One-Time Password Algorithm](https://datatracker.ietf.org/doc/html/rfc4226)
- [RFC 6238: TOTP: Time-Based One-Time Password Algorithm](https://datatracker.ietf.org/doc/html/rfc6238)
- [OWASP: Authentication Cheat Sheet - One-Time Passwords](https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html#one-time-passwords)
- [Wikipedia: One-time password](https://en.wikipedia.org/wiki/One-time_password)