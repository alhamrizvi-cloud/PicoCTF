### **The Goal**
Extract and decrypt a hidden file transferred over a specific TCP stream in a network capture (`.pcap`) file.

### **The Process**
1.  **Locate the Traffic:** Open the PCAP in Wireshark and filter for traffic on **Port 2009**. Follow the **TCP Stream** to see the conversation.
2.  **Identify the Data:** Look for a packet containing a large block of data. You’ll know you have the right part if the raw data starts with the ASCII characters **`Salted__`**.
3.  **Extract the File (Crucial Step):** * Do **not** copy-paste the text manually (this often corrupts the binary formatting).
    * In Wireshark, change the view from "ASCII" to **"Raw"**.
    * Click **"Save as..."** to export the raw bytes directly as a file (e.g., `data.des3`).
    * *Alternative:* Use [HexEd.it](https://hexed.it/) to paste the hex and export it as a binary file to ensure no data is lost.
4.  **Run the Command:** Execute the decryption command provided in the challenge hint while in the same folder as your saved file.
