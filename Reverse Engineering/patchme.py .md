We analyzed the Python script and found that it checks for a password before showing the flag. The password was split into multiple strings and compared inside an `if` condition.

To bypass this check, we commented out the entire `if` block using triple quotes (`'''`). Then we directly executed the flag decryption code outside the condition.

The script uses an XOR function with the key **"utilitarian"** to decrypt the contents of `flag.txt.enc`.

After running the modified script, the decrypted flag was printed successfully.

<img width="1268" height="775" alt="image" src="https://github.com/user-attachments/assets/9a8c94ca-2dd8-42aa-a70c-acf58e9b9fc9" />
