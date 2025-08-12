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

### ✅ Step-by-step Fix for Windows & Python 3.13
🛠️ Step 1: Manually verify certifi and Python SSL
Run this script to make sure Python sees the correct certificate bundle:
<img width="719" height="123" alt="image" src="https://github.com/user-attachments/assets/da8e1b81-cc76-4881-8dc3-456f0028609f" />
✅ You should get valid .pem file paths for both.
### 🛠️ Step 2: Copy certifi CA bundle to default SSL path
If Python isn’t using the certifi path by default, you can force it to by copying certifi's .pem file over the default.

Run this in Python to find the paths:
<img width="503" height="175" alt="image" src="https://github.com/user-attachments/assets/c8f3a916-6869-44f5-8692-150b05bf0d1a" />
2. Manually copy the .pem file from the certifi location to the default SSL trust file location.

🧠 Why?
This forces Python’s built-in ssl module to trust the right root CAs when using neo4j+s://.
### ✅ Step 3: Test clean neo4j+s:// connection (no ssl_context)
<img width="628" height="388" alt="image" src="https://github.com/user-attachments/assets/7ab9cbfe-c92c-4bf8-8a6a-38dbe06fb4ca" />

If it works — awesome! You're using neo4j+s:// securely with full cert validation.

🧪 If Still Not Working
Run this to print the exact TLS error:
<img width="675" height="517" alt="image" src="https://github.com/user-attachments/assets/67f9ef69-a361-4408-b0e4-b1781dd86862" />


### ✅ Recommendation
Always use neo4j+s:// for secure, verified TLS connections (especially with Neo4j Aura)

Ensure your Python environment trusts the correct root CAs

Avoid disabling SSL certificate checks (neo4j+ssc://) in production

1. Locate the `certifi` CA file (usually here):

