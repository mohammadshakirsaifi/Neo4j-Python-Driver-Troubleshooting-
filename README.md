# 🛠️ Neo4j Python Driver: Troubleshooting `neo4j+s://` TLS Connection Issues

## ✅ Problem Summary

- A working Neo4j Aura connection using `neo4j+s://` fails on one laptop but works on another.
- The same credentials and code work fine using `neo4j+ssc://` or `bolt://` with manual SSL config.
- TLS errors or `ServiceUnavailable: Unable to retrieve routing information` are raised.

---

## ✅ Root Cause

- Python’s `ssl` module is not trusting the correct root certificate authorities (CAs).
- On the failing laptop, Python is looking for certificates at:

## C:\Program Files\Common Files\SSL\cert.pem

- That file is either missing or contains an incomplete list of trusted CAs.
- On the working laptop, Python uses the `certifi` CA bundle, which includes up-to-date root CAs.

---

## ✅ Resolution Options

### 🔧 Option 1: Copy `certifi` CA file to Python’s default cert location
1. Locate the certifi CA file (usually here):
 <img width="1229" height="67" alt="image" src="https://github.com/user-attachments/assets/696e7df3-7182-403f-a72c-f8a6c727402a" />

2. Copy it to this location:
<img width="415" height="50" alt="image" src="https://github.com/user-attachments/assets/602481af-2f6c-4462-8c28-c6d589326d95" />

3. If the folder doesn’t exist, create it.

4. Run the terminal or file copy operation with **administrator permissions**.

---

### 🔧 Option 2: Set environment variable `SSL_CERT_FILE` to force Python to use `certifi`

#### ✅ Temporary (current PowerShell session):

$env:SSL_CERT_FILE = "C:\Path\To\Python\Lib\site-packages\certifi\cacert.pem"
python your_script.py
#### ✅ Permanent Setup:
<img width="457" height="170" alt="image" src="https://github.com/user-attachments/assets/15f02263-f217-4b33-9eae-e2f10c8bd7d2" />

#### ✅ Final Working Code (No manual SSL config)
<img width="637" height="317" alt="image" src="https://github.com/user-attachments/assets/820b0f8a-c6a0-4fa9-9060-0a1350e4a2bf" />

### ✅ Alternative: Use bolt:// with manual SSL context
<img width="874" height="426" alt="image" src="https://github.com/user-attachments/assets/12bcc3b8-66a1-4989-a4be-37186153be0a" />

### 🧪 Bonus: Diagnostic Script
Use this to check what certs your Python is using:
<img width="773" height="159" alt="image" src="https://github.com/user-attachments/assets/9c4f08e5-3974-4f7c-bb1e-abcf0b2bc5b7" />

### ✅ Recommendation
Always use neo4j+s:// for secure, verified TLS connections (especially with Neo4j Aura)

Ensure your Python environment trusts the correct root CAs

Avoid disabling SSL certificate checks (neo4j+ssc://) in production

1. Locate the `certifi` CA file (usually here):

