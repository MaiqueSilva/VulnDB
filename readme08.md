🛡️ Stored XSS Vulnerability in Samarium - Business Management System (v0.9.6)

Github: https://github.com/oitcode/samarium

📌 Summary

A stored Cross-Site Scripting (XSS) vulnerability was discovered in the open-source project Samarium - Business Management System, version v0.9.6. The vulnerability allows an attacker to inject malicious JavaScript code via the product image upload feature, resulting in code execution when the image is viewed—even by unauthenticated users.

🕵️‍♂️ Vulnerability Details

During a security assessment of the application, it was identified that the image upload feature for products accepts SVG files without proper sanitization. An attacker can exploit this by uploading a specially crafted .svg file containing embedded JavaScript. When this file is rendered in a browser, the malicious script executes in the context of the victim’s session.

🔬 Steps to Reproduce

✅ Step 1 — Upload Malicious File

While creating a new product, the application provides an option to upload a product image. The following malicious SVG was uploaded:

```svg
<svg version="1.1" baseProfile="full" xmlns="http://www.w3.org/2000/svg">
  <polygon id="triangle" points="0,0 0,50 50,0" fill="#009900"
stroke="#004400"/>
  <script type="text/javascript">
    alert(document.cookie);
  </script>
</svg>
```

<img width="1906" height="838" alt="2" src="https://github.com/user-attachments/assets/31ab3298-aed0-4c7d-ba97-c32dee1724fd" />


🧾 Evidence 1

Application accepts the uploaded SVG file without validation or sanitization.

<img width="1909" height="841" alt="3" src="https://github.com/user-attachments/assets/5a727d87-7c69-4688-ba08-7ad9cf93df39" />


✅ Step 2 — Access Product Page

Navigate to the main product listing page (/products) without authentication.

🧾 Evidence 2

The newly created product is publicly listed.

<img width="1905" height="825" alt="4" src="https://github.com/user-attachments/assets/48cd9fe3-a01e-482e-a34c-f6a6950b45d5" />


✅ Step 3 — Open Image in New Tab

Right-click the product image and open it in a new browser tab.

<img width="1910" height="835" alt="5" src="https://github.com/user-attachments/assets/e8498c0c-623c-4380-a3da-47e11fa3fb1a" />


🧾 Evidence 3

The browser renders the SVG file, executing the embedded JavaScript.

<img width="1564" height="842" alt="6" src="https://github.com/user-attachments/assets/882ae7f8-64e9-4492-a0a8-7f88e1c49a8b" />


💥 Impact

Stored XSS: Malicious scripts persist in the application and are executed in the browsers of any user who accesses the image.

Session Hijacking: The script can be used to exfiltrate session tokens (e.g., via document.cookie).

Unauthenticated Exploitation: No login is required to trigger the vulnerability.

High Severity: All users are at risk, including administrators.

🔧 Recommended Fix

Block SVG uploads or sanitize SVG content server-side using tools like DOMPurify.

Implement a MIME type check and file content validation.

Serve user-uploaded files from a different domain or with Content-Type: application/octet-stream to prevent execution.

