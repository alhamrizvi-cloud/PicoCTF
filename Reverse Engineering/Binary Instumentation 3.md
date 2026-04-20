# Binary Instrumentation 3.md

I used **binwalk** to extract the hidden data from the `.exe` file. After extraction, it gave me a file named `6000`.

Instead of wasting time creating things like `payload.bin` or `atom.bin` on Windows, I directly opened the `6000` file in **Ghidra**.

Inside Ghidra, I went to the **Program Tree** and opened the `.rdata` section.

That’s it no need for overcomplicated steps.

The Base64 strings were already sitting there inside `.rdata`.

<img width="1416" height="743" alt="image" src="https://github.com/user-attachments/assets/d3ada4ee-d880-4097-9d31-42f3dc4bef6a" />

<img width="1416" height="743" alt="image" src="https://github.com/user-attachments/assets/8bc35072-a9f8-4a35-aaa2-b33a59253011" />
