# 11n8n Data Sanitization Pipeline

# 🧹 Project 11: Automated Data Sanitization Pipeline with JavaScript in n8n

![n8n](https://img.shields.io/badge/n8n-FF6D5W?style=for-the-badge&logo=n8n&logoColor=white)
![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black)

<img width="1192" height="609" alt="n8n data sanitization pipeline" src="https://github.com/user-attachments/assets/2cfcbedc-99d8-4e35-ba71-173665dc9baa" />

## 📖 Overview
An advanced data transformation pipeline built in n8n using a custom JavaScript Code Node to clean, sanitize, and standardize raw, messy input data from public web forms and legacy systems.

## 🏢 The Business Problem
Public registration forms and legacy systems often capture dirty data, such as:
- random leading or trailing whitespace
- inconsistent casing (for example, all lowercase or unformatted names)
- unstandardized phone numbers
- inconsistent email formatting

This leads to duplicate customer records, poor CRM hygiene, and operational inefficiency.

## 💡 The Solution
An automated, high-performance data transformation pipeline executed entirely within an n8n Code Node in milliseconds.

This pipeline:
- removes stray leading and trailing whitespace with `.trim()`
- normalizes names to title case using ES6 array methods (`split()`, `map()`, `join()`)
- standardizes email addresses to lowercase
- removes non-numeric characters from phone numbers using regex
- converts local Indonesian numbers like `0812...` into the international WhatsApp format `+62...`
- handles missing or inconsistent JSON keys safely with dynamic fallback logic

## 🛠️ Technical Implementation
```javascript
for (const item of $input.all()) {
  // Menggunakan pencarian dinamis untuk mengantisipasi spasi tersembunyi pada key JSON
  const rawNama = item.json.nama_pendaftar || "";
  const rawEmail = item.json.email_pendaftar || "";
  const rawHp = item.json.nomor_hp || item.json["nomor_hp "] || item.json.no_hp || "";

  // 1. SANITASI NAMA (Trim & Title Case)
  const cleanNama = rawNama
    .trim()
    .toLowerCase()
    .split(' ')
    .filter(word => word.length > 0)
    .map(word => word.charAt(0).toUpperCase() + word.slice(1))
    .join(' ');

  // 2. SANITASI EMAIL (Trim & Lowercase)
  const cleanEmail = rawEmail.trim().toLowerCase();

  // 3. SANITASI NOMOR HP / WHATSAPP (Regex & Konversi +62)
  const digitsOnly = String(rawHp).replace(/\D/g, '');
  let cleanHp = digitsOnly;

  if (digitsOnly.startsWith('0')) {
    cleanHp = '+62' + digitsOnly.slice(1);
  } else if (digitsOnly.startsWith('62')) {
    cleanHp = '+' + digitsOnly;
  }

  // Masukkan kembali ke payload JSON yang bersih
  item.json.sanitized_data = {
    nama: cleanNama,
    email: cleanEmail,
    whatsapp: cleanHp
  };
}

return $input.all();
```

## 📈 Business Value
- Pristine database hygiene: eliminates formatting errors and ensures uniform customer profiles across all touchpoints
- Zero administrative overhead: replaces manual data cleaning tasks with real-time automated processing
- Seamless integration: delivers production-ready, standardized JSON objects ready for SQL databases, Google Sheets, or CRM ingestion

## 🚀 Technical Stack
- n8n (Workflow Automation Platform)
- Custom JavaScript (ES6 array methods, regex pattern matching)
- Dynamic JSON payload mapping

> Note: In collaboration with Gemini AI Pro
