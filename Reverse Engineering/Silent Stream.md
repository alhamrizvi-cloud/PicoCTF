
We are given a `.pcap` file containing captured network traffic. The goal is to recover a hidden flag from this traffic.

Step 1: Analyze the PCAP

Instead of manually analyzing the capture using Wireshark, we used Scapy to automate extraction of TCP data.

Step 2: Extract TCP Streams

We wrote a script to parse packets, group them into TCP flows, and reassemble payloads based on sequence numbers.

Key idea: TCP data is fragmented, so we must sort segments by sequence number, handle overlaps, and rebuild the full byte stream.

```python
def reassemble_tcp_segments(segments):
    segments = sorted(segments, key=lambda x: x[0])
    result = bytearray()
    current_seq = None

    for seq, data in segments:
        if not data:
            continue

        if current_seq is None:
            result.extend(data)
            current_seq = seq + len(data)
            continue

        if seq + len(data) <= current_seq:
            continue

        if seq < current_seq:
            overlap = current_seq - seq
            data = data[overlap:]
            seq = current_seq

        result.extend(data)
        current_seq = seq + len(data)

    return bytes(result)
```

Run:

```bash
python3 test.py packets.pcap -o flag.enc
```

Output:

```bash
[+] Selected stream: 10.10.10.10:12345 -> 10.10.10.11:9000
[+] Reassembled 9126 bytes
[+] Written to: flag.enc
```

Step 3: Analyze Extracted Data
The extracted file `flag.enc` is not readable, indicating it is encoded or encrypted.

Step 4: Identify Encoding
By testing simple transformations, we determine that each byte is shifted by a fixed value:

```python
decoded_byte = (byte - key) % 256
```

This is a byte-wise Caesar shift with key = 42.

Step 5: Decrypt
Script:

```python
import argparse
from pathlib import Path


def decode_byte(b, key):
    return (b - key) % 256


def decrypt_file(input_file, output_file, key=42):
    print(f"[!] Decrypting '{input_file}' with key = {key}")

    with open(input_file, "rb") as f:
        data = f.read()

    decoded_data = bytes(decode_byte(b, key) for b in data)

    with open(output_file, "wb") as f:
        f.write(decoded_data)

    print(f"[+] Decrypted {len(data)} bytes into '{output_file}'")


def main():
    parser = argparse.ArgumentParser(description="Decode the extracted stream")
    parser.add_argument(
        "-i",
        "--input",
        default="flag.enc",
        help="Path to encoded input (default: flag.enc)",
    )
    parser.add_argument(
        "-o",
        "--output",
        default="flag_decoded.jpg",
        help="Path to decoded output (default: flag_decoded.jpg)",
    )
    parser.add_argument(
        "-k",
        "--key",
        type=int,
        default=42,
        help="Decoding key (default: 42)",
    )
    args = parser.parse_args()

    base_dir = Path(__file__).resolve().parent
    input_path = Path(args.input)
    output_path = Path(args.output)

    if not input_path.is_absolute() and not input_path.exists():
        input_path = base_dir / input_path
    if not output_path.is_absolute():
        output_path = base_dir / output_path

    decrypt_file(str(input_path), str(output_path), key=args.key)


if __name__ == "__main__":
    main()

```

Run:

```bash
python3 test2.py -i flag.enc -o flag_decoded.jpg -k 42
```

Output:

```bash
[+] Decrypted 9126 bytes into 'flag_decoded.jpg'
```

Step 6: Recover Flag
Open the resulting image file to view the flag.

Final Flag:

```
picoCTF{tr4ck_th3_tr4ff1c_35c6df29}
```

Key Takeaways
Automating PCAP analysis is often faster than manual inspection. TCP streams must be properly reassembled before analysis. Many challenges use simple encoding schemes like byte shifts. Combining network analysis with basic cryptography is common in CTF problems.

Summary
Reassembled TCP stream, extracted encoded data, reversed byte shift, recovered image, obtained flag.


