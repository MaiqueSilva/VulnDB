Arbitrary JavaScript Execution via Unsanitized SVG Upload in Samarium - Business Management System v0.9.6 /cms/webpage/

GitHub: https://github.com/oitcode/samarium

📌 Summary

A stored Cross-Site Scripting (XSS) vulnerability was discovered in the open-source project Samarium - Business Management System, version v0.9.6. The vulnerability allows an attacker to inject malicious JavaScript code via the webpage image upload feature, resulting in code execution when the image is viewed — even by unauthenticated users.

🕵️‍♂️ Vulnerability Details

During a security assessment of the application, it was identified that the image upload feature for page creation at /cms/webpage/ accepts SVG files without proper sanitization. An attacker can exploit this by uploading a specially crafted .svg file containing embedded JavaScript. When this file is rendered in a browser, the malicious script executes in the context of the victim’s session.

🔬 Steps to Reproduce

✅ Step 1 — Upload Malicious File

While creating a new page or editing an existing one in /cms/webpage/, the application provides an option to upload an image, which is stored in /gallery/. The following malicious SVG was uploaded:

```
svg
<svg version="1.1" baseProfile="full" xmlns="http://www.w3.org/2000/svg">
  <polygon id="triangle" points="0,0 0,50 50,0" fill="#009900"
stroke="#004400"/>
  <script type="text/javascript">
    alert(document.cookie);
  </script>
</svg>
```



🧾 Evidence 1

The application accepts the uploaded SVG file without validation or sanitization.

<img width="1914" height="884" alt="1" src="https://github.com/user-attachments/assets/59c9a8a7-7bc4-4a6c-87e4-4719368812ff" />

<img width="1910" height="848" alt="3" src="https://github.com/user-attachments/assets/a7e0c2f7-b082-4656-860d-6a68118a089c" />


<img width="1918" height="887" alt="4" src="https://github.com/user-attachments/assets/58a1bade-4192-42dc-930a-c4cc49bea6de" />


✅ Step 2 — Access Uploaded Image

Navigate to /gallery/ to locate the uploaded SVG file.

The uploaded file is accessible without authentication.

✅ Step 3 — Open Image in New Tab

Right-click the uploaded SVG and open it in a new browser tab.


<img width="1918" height="884" alt="5" src="https://github.com/user-attachments/assets/3d2c4d64-1c43-41fd-a50f-83af596e6b8a" />


🧾 Evidence 2

The browser renders the SVG file, executing the embedded JavaScript.

<img width="1913" height="844" alt="6" src="https://github.com/user-attachments/assets/cf74a0f5-f287-45fb-a920-e9328dca4779" />

💥 Impact

Stored XSS: Malicious scripts persist in the application and are executed in the browsers of any user who accesses the image.

Session Hijacking: The script can be used to exfiltrate session tokens (e.g., via document.cookie).

Unauthenticated Exploitation: No login is required to trigger the vulnerability.

High Severity: All users are at risk, including administrators.

🔧 Recommended Fix

Block SVG uploads or sanitize SVG content server-side using tools like DOMPurify.

Implement MIME type and file content validation before accepting uploads.

Serve user-uploaded files from a separate domain or enforce Content-Type: application/octet-stream to prevent execution.


