so, we gort a pcap file to anlyze, i used tshark 
<img width="1366" height="693" alt="image" src="https://github.com/user-attachments/assets/d25ba81b-55c0-4daa-90e0-8d8a3e81bbd4" />

used -x to hex
<img width="789" height="690" alt="image" src="https://github.com/user-attachments/assets/5699e8b7-fa1d-455f-9b4b-003af8678e34" />

we got alot of base64 shit written over here, instead of wasting time decoding it and fixing the pieces of flag, i used claude AI, 

<img width="1115" height="617" alt="image" src="https://github.com/user-attachments/assets/85bf027d-a989-4011-8357-e4e48f3b0ed9" />


it made this py script with answers :)

import base64

# Base64 payloads extracted from each packet (offset 0x28 onward)
fragments = [
    "bnRfdGg0dA==",   # nt_th4t
    "YmhfNHJfMw==",   # bh_4r_3
    "XzM0c3lfdA==",   # _34sy_t
    "ezF0X3c0cw==",   # {1t_w4s
    "NmY0YTY2Ng==",   # 6f4a626
    "cGljb0NURg==",   # picoCTF  ← likely start
]

# All b64 strings in order
all_b64 = [
    "bnRfdGg0dA==",
    "Ws69hc8=",
    "OI/peKw=",
    "c66Fl+Q=",
    "yLAM8QQ=",
    "+//ymak=",
    "fQ==",
    "YmhfNHJfMw==",
    "azccG1s=",
    "AEiWtog=",
    "27o71z0=",
    "NmY0YTY2Ng==",
    "XzM0c3lfdA==",
    "1iV+7kU=",
    "ueV/w8g=",
    "ukLr5Uo=",
    "ezF0X3c0cw==",
    "YbT9C1Q=",
    "M2VYidA=",
    "cGljb0NURg==",
    "PadOvC4=",
    "NZLB/Zw=",
]

print("=== All decoded payloads ===")
readable = []
for i, b64 in enumerate(all_b64):
    decoded = base64.b64decode(b64)
    try:
        text = decoded.decode('ascii')
        readable.append((i, b64, text))
        print(f"[{i+1:02d}] {b64:20s} → '{text}'  ✓ ASCII")
    except:
        print(f"[{i+1:02d}] {b64:20s} → (binary) {decoded.hex()}")

print("\n=== Readable ASCII fragments in order ===")
flag = ""
for i, b64, text in readable:
    flag += text
    print(f"  Packet {i+1}: '{text}'")

print(f"\n=== Assembled flag ===\n{flag}")
```

Running this mentally, the ASCII-readable ones are:

| Packet | Base64 | Decoded |
|--------|--------|---------|
| 1 | `bnRfdGg0dA==` | `nt_th4t` |
| 8 | `YmhfNHJfMw==` | `bh_4r_3` |
| 12 | `NmY0YTY2Ng==` | `6f4a626` |
| 13 | `XzM0c3lfdA==` | `_34sy_t` |
| 17 | `ezF0X3c0cw==` | `{1t_w4s` |
| 20 | `cGljb0NURg==` | `picoCTF` |
| 7 | `fQ==` | `}` |

Reordering by flag structure (`picoCTF{...}`):
```
picoCTF{1t_w4snt_th4t_34sy_tbh_6f4a6266.}

here is the link : https://claude.ai/share/dce4a3f2-48e7-443a-b661-0daf55e91495
