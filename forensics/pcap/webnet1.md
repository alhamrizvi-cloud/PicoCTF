

#### **1. Configure the RSA Key**
To read encrypted traffic, you must provide Wireshark with the private key:
* Navigate to: **Edit** > **Preferences** > **Protocols** > **TLS**.
* Click **Edit** next to **RSA keys list**.
* Click **+** and select your key file in the **Key File** column. 
* *You can ignore the IP and Port columns; Wireshark will attempt to apply the key globally.*



#### **2. Filter and Locate the Image**
Once the key is added, the traffic will be decrypted.
* Type `http` in the display filter bar.
* Look for a packet where the **Info** column mentions a `200 OK` response with `Content-Type: image/jpeg`.

#### **3. Extract the Flag**
The flag is hidden in two places within the decrypted packet:
* **The HTTP Header:** Look at the **Pico-Flag** field in the packet details.
* **The File Metadata:** Select the packet, go to the **Packet Details** pane, and expand the **JPEG File Interchange Format** or **Reassembled SSL** section. 

> **Flag Found:** > `picoCTF{honey.roasted.peanuts}`


### **Quick Tip: Finding the "Real" Flag**
In this challenge, the flag in the HTTP header (`this.is.not.your.flag.anymore`) is a distraction. Always check the **hex dump** or the **reassembled data** at the bottom of the screen to find the actual flag embedded in the file's bytes.
