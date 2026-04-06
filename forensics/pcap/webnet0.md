**Goal:** Decrypt captured TLS traffic using a provided RSA private key to find the hidden flag.

#### **1. Import the RSA Key**
Open Wireshark and navigate to the RSA Key List settings:
* **Path:** `Edit` > `Preferences` > `Protocols` > `TLS`
* Locate the **RSA keys list** and click **Edit**.
* Click the **+ (plus)** icon to add a new entry.
* **Key File:** Click the folder icon to select your `.key` or `.pem` file.
* *Note: You can leave the IP Address and Port fields blank.*



#### **2. Locate the Decrypted Traffic**
Once the key is added, Wireshark will automatically decrypt the traffic. 
* Look for packets now labeled with the **HTTP** protocol (previously hidden inside TLS).
* Right-click an HTTP packet and select **Follow** > **TLS Stream** or **HTTP Stream**.

#### **3. Find the Flag**
The flag is located within the HTTP response headers of the decrypted traffic.

> **Flag Found:** > `picoCTF{nongshim.shrimp.crackers}`

### **Why this works**
Normally, TLS traffic is encrypted and unreadable. By providing Wireshark with the **RSA Private Key**, it can decrypt the session. Once decrypted, the "Application Data" is revealed as standard HTTP text, allowing you to see custom headers like `Pico-Flag`.
