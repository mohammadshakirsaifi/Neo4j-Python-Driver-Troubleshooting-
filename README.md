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
C:\Users<YourUsername>\AppData\Local\Programs\Python\PythonXXX\Lib\site-packages\certifi\cacert.pem

2. Copy it to this location:

C:\Program Files\Common Files\SSL\cert.pem

3. If the folder doesn’t exist, create it.

4. Run the terminal or file copy operation with **administrator permissions**.

---

### 🔧 Option 2: Set environment variable `SSL_CERT_FILE` to force Python to use `certifi`

#### ✅ Temporary (current PowerShell session):

$env:SSL_CERT_FILE = "C:\Path\To\Python\Lib\site-packages\certifi\cacert.pem"
python your_script.py
#### ✅ Permanent Setup:
1. Open System Properties > Environment Variables
2. Under User variables, click New
3. Set:

### Name: SSL_CERT_FILE
Value: Full path to cacert.pem from certifi (e.g., C:\Path\To\Python\Lib\site-packages\certifi\cacert.pem)

4. Click OK and restart any terminal/editor sessions.

#### ✅ Final Working Code (No manual SSL config)
from neo4j import GraphDatabase

uri = "neo4j+s://f5d5c5a2.databases.neo4j.io"
user = "neo4j"
password = "your_password_here"

driver = GraphDatabase.driver(uri, auth=(user, password))
with driver.session(database="neo4j") as session:
    result = session.run("RETURN 'Connected to Neo4j!' AS message")
    print(result.single()["message"])
driver.close()

### ✅ Alternative: Use bolt:// with manual SSL context
import ssl
import certifi
from neo4j import GraphDatabase

uri = "bolt://f5d5c5a2.databases.neo4j.io:7687"
user = "neo4j"
password = "your_password_here"

ssl_context = ssl.create_default_context(cafile=certifi.where())

driver = GraphDatabase.driver(uri, auth=(user, password), encrypted=True, ssl_context=ssl_context)
with driver.session(database="neo4j") as session:
    result = session.run("RETURN 'Connected to Neo4j!' AS message")
    print(result.single()["message"])
driver.close()
### 🧪 Bonus: Diagnostic Script
Use this to check what certs your Python is using:
import ssl
import certifi

print("Certifi CA Path:", certifi.where())
print("Python Default SSL CA Path:", ssl.get_default_verify_paths().openssl_cafile)
### ✅ Recommendation
Always use neo4j+s:// for secure, verified TLS connections (especially with Neo4j Aura)

Ensure your Python environment trusts the correct root CAs

Avoid disabling SSL certificate checks (neo4j+ssc://) in production

1. Locate the `certifi` CA file (usually here):

