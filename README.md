# 11n8n-data-sanitization-pipeline

# 🧹 Project 12: Automated Data Sanitization Pipeline with JavaScript in n8n

![n8n](https://img.shields.io/badge/n8n-FF6D5W?style=for-the-badge&logo=n8n&logoColor=white)
![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black)

<img width="1192" height="609" alt="image" src="https://github.com/user-attachments/assets/2cfcbedc-99d8-4e35-ba71-173665dc9baa" />


## 📖 Overview
An advanced data transformation pipeline built in **n8n** utilizing custom **JavaScript (Code Node)** to clean, sanitize, and standardize raw, messy input data from public web forms or legacy systems before downstream CRM or database ingestion.

## 🏢 The Business Problem
Public registration forms and legacy systems frequently capture "dirty data"—random leading/trailing whitespaces, inconsistent casing (all lowercase or unformatted names), and unstandardized phone number formats. Manual data cleaning in spreadsheets introduces administrative bottlenecks, delays follow-ups, and pollutes downstream databases.

## 💡 The Solution
An automated, high-performance data transformation pipeline executed entirely within an n8n Code Node in milliseconds:
- **Whitespace Purging:** Dynamically drops stray leading/trailing spaces using `.trim()`.
- **Title Case Normalization:** Standardizes names using ES6 array methods (`.split()`, `.map()`, `.join()`).
- **Email Normalization:** Forces lowercase formatting to prevent duplicate CRM entries.
- **Regex-Based Phone Formatting:** Strips non-numeric characters and automatically converts local Indonesian numbers (`08...`) into the international WhatsApp standard (`+62...`).
- **Error-Resilient Key Fallback:** Dynamically handles variations or hidden whitespaces in incoming JSON payload keys.

## 🛠️ Technical Implementation (JavaScript Code Snippet)
```javascript
for (let item of $input.all()) {
  // Menggunakan pencarian dinamis untuk mengantisipasi spasi tersembunyi pada key JSON
  let rawNama = item.json.nama_pendaftar || "";
  let rawEmail = item.json.email_pendaftar || "";
  let rawHp = item.json.nomor_hp || item.json["nomor_hp "] || item.json.no_hp || "";

  // 1. SANITASI NAMA (Trim & Title Case)
  let cleanNama = rawNama.trim()
    .toLowerCase()
    .split(' ')
    .filter(word => word.length > 0)
    .map(word => word.charAt(0).toUpperCase() + word.slice(1))
    .join(' ');

  // 2. SANITASI EMAIL (Trim & Lowercase)
  let cleanEmail = rawEmail.trim().toLowerCase();

  // 3. SANITASI NOMOR HP / WHATSAPP (Regex & Konversi +62)
  let digitsOnly = String(rawHp).replace(/\D/g, ''); 
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


📈 Business Value
Pristine Database Hygiene: Eliminates formatting errors and ensures uniform customer profiles across all touchpoints.

Zero Administrative Overhead: Replaces manual data cleaning tasks with real-time automated processing.

Seamless Integration: Delivers production-ready, standardized JSON objects primed for SQL databases, Google Sheets, or CRM ingestion.

🚀 Technical Stack
n8n (Workflow Automation Platform)

Custom JavaScript (ES6 Array Methods, Regex Pattern Matching)

Dynamic JSON Payload Mapping

Noted : Incollaboration with Gemini AI Pro
