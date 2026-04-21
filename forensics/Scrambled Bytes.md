### scrambled-bytes — Writeup

We are given:

* A **pcap file** (network capture)
* A Python script that was used to send the data

Goal:

> Recover the original file (flag) from scrambled network packets.

---

### Step 1 — Understand the Script

Looking at the script:

```python
payload = bytearray(f.read())
random.seed(int(time()))
random.shuffle(payload)
```

What happens:

1. File is read into bytes
2. Bytes are **shuffled randomly**

---

Then for each byte:

```python
UDP(sport=random.randrange(65536)) /
Raw(load=bytes([b ^ random.randrange(256)]))
```

So each byte is:

* XORed with a random number
* Sent as **1 byte per UDP packet**
* With a random **source port**

---

### Key Problems

We need to reverse:

1. XOR encryption
2. Shuffling

---

### Step 2 — Look at Network Capture

Filter useful packets:

```bash
tshark -r capture.pcapng -Y "udp && data.len==1"
```

We notice:

* Source: `172.17.0.2`
* Destination: `172.17.0.3`
* Each packet = **1 byte**

So these are our encoded bytes.

---

### Step 3 — Extract Packets

We write a script to collect:

* Source port
* Payload byte

```python
from scapy.all import *

packets = rdpcap('capture.pcapng')
payload_packets = []

for p in packets:
    if p.haslayer(IP) and p.haslayer(UDP):
        if p[IP].src == "172.17.0.2" and p[IP].dst == "172.17.0.3":
            if p[UDP].dport == 56742 and len(p[UDP].payload) == 1:
                payload_packets.append((p[UDP].sport, bytes(p[UDP].payload)))
```

We get around **1992 packets**.

---

### Step 4 — Break the Randomness

Important line in original script:

```python
random.seed(int(time()))
```

This means:

> Random values depend on system time

---

### Step 5 — Recover the Seed

From the first packet:

```text
Epoch Time: 1614044650
```

So:

```python
random.seed(1614044650)
```

---

### Step 6 — Reproduce Original Random Flow

Original order:

1. shuffle(payload)
2. generate sport
3. generate XOR value

We must follow **same order**.

---

### Step 7 — Reverse the Process

```python
import random

random.seed(1614044650)

n = len(payload_packets)

# recreate shuffle positions
positions = list(range(n))
random.shuffle(positions)

output = [0] * n

for i, (sport, data) in enumerate(payload_packets):
    # generate same random port
    rand_port = random.randrange(65536)

    # sanity check
    if rand_port != sport:
        raise Exception("Mismatch")

    # reverse XOR
    original_byte = data[0] ^ random.randrange(256)

    # put byte back in correct position
    output[positions[i]] = original_byte

# save file
with open("output.bin", "wb") as f:
    f.write(bytes(output))
```
### Step 8 — Get the Flag

Check file type:

```bash
file output.bin
```

Output:

```text
PNG image data
```

Open it → it shows the flag:

```text
picoCTF{n0_t1m3_t0_w4st3_5hufflin9_ar0und}
``
