Arbitrary JavaScript Execution via Unsanitized SVG Upload in Samarium - Business Management System v0.9.6 /dashboard/team/

GitHub: https://github.com/oitcode/samarium

📌 Summary

A vulnerability has been identified in the open source project Samarium - Business Management System, version v0.9.6. During testing, it was possible to upload malicious files with the .svg extension, which could lead to a system compromise.

🕵️‍♂️ Vulnerability Details

During a security assessment of the application, it was identified that the image upload feature in /dashboard/team (used for creating a new team) accepts SVG files without proper sanitization. An attacker can exploit this by uploading a specially crafted .svg file containing embedded JavaScript. When this file is rendered in a browser, the malicious script executes in the context of the victim’s session.
🔬 Steps to Reproduce

✅ Step 1 — Upload Malicious File

Navigate to /dashboard/team and choose the option to create a new team.

<img width="1914" height="849" alt="1" src="https://github.com/user-attachments/assets/dea7e7fc-6842-480b-b1d5-a53a410b19c0" />


During the creation process, use the image upload field to upload a malicious SVG file.

<img width="1919" height="872" alt="3" src="https://github.com/user-attachments/assets/4fa1d5e3-4523-4a6f-8a81-f1226d873d31" />


Example payload:

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

The SVG file is uploaded successfully without validation or sanitization.

✅ Step 2 — Open Uploaded Image

Access the uploaded image file directly and open it in a new browser tab.

<img width="1920" height="878" alt="4" src="https://github.com/user-attachments/assets/99aba919-5aad-48b6-ae26-b3199ea6aa32" />

🧾 Evidence 2

The browser renders the SVG file and executes the embedded JavaScript.

<img width="1920" height="876" alt="5" src="https://github.com/user-attachments/assets/f418af3b-b771-409a-aa0d-86012900d489" />

💥 Impact

Stored XSS: The malicious script is stored in the application and executed whenever a user accesses the image.

Session Hijacking: Attackers can steal session cookies or other sensitive data.

Unauthenticated Exploitation: No authentication is required to trigger the payload.

High Severity: All users are at risk, including administrators.

🔧 Recommended Fix

Restrict or block SVG uploads entirely.

Implement server-side sanitization of SVG files using secure libraries like DOMPurify.

Enforce strict MIME type and content validation for uploaded files.

Serve uploaded files from a separate domain or with Content-Type: application/octet-stream to prevent in-browser execution.
