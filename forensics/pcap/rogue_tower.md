In UDP stream 5, the attacker attempts to connect using an unauthorized IMSI number. We note this IMSI because it will later be used as the encryption key.

Next, we analyze the HTTP traffic, specifically streams 6 to 10. These streams contain the exfiltrated data sent via multiple POST requests.

From these packets, we extract a Base64-encoded string. After decoding it (e.g., using CyberChef), the output appears as unreadable binary/gibberish, indicating that it is still encrypted.

Based on the hint, the encryption key is derived from the IMSI. Instead of using the full IMSI, we take the last few digits (rightmost part) of the IMSI (e.g., last 8 digits) as the key.

We then perform an XOR decryption using this key on the decoded data. This successfully reveals the flag.
