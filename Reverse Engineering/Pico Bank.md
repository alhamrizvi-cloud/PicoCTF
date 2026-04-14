
A banking Android app that hides a flag across two locations: binary-encoded transaction values and a server OTP endpoint.

## Step 1 — Decompile the APK
Open JADX-GUI and drag in `picobank.apk`. Navigate to:
```
com.example.picobank → Login
```
You'll find hardcoded credentials:
- **Username:** `johnson`
- **Password:** `tricky1990`

## Step 2 — Part 1: Binary Transactions
In `MainActivity`, transaction amounts are suspiciously binary-looking. As the notification hints:

![Notification hint](https://github.com/user-attachments/assets/d56d7880-4cc9-4110-b499-1ef18b2392fb)

Convert each binary amount to decimal → ASCII:

```python
transactions = [
    "1110000",  "1101001", "1100011", "1101111",
    "1000011",  "1010100", "1000110", "1111011",
    "110001",   "1011111", "1101100", "110001",
    "110011",   "1100100", "1011111", "110100",
    "1100010",  "110000",  "1110101", "1110100",
    "1011111",  "1100010", "110011",  "110001",
    "1101110",  "1100111", "1011111"
]
result = ''.join(chr(int(b, 2)) for b in transactions)
print(result)
# → picoCTF{1_l13d_4b0ut_b31ng_
```

## Step 3 — Part 2: OTP Endpoint
In the `OTP` class, find the hardcoded OTP `9673` and the `/verify-otp` endpoint. Send:

```bash
curl -X POST http://amiable-citadel.picoctf.net:58539/verify-otp \
  -H "Content-Type: application/json" \
  -d '{"otp":"9673"}'
```

Response:

![Server response](https://github.com/user-attachments/assets/90b0c23c-83ba-4cd7-b43e-0c3fee90ba65)

```json
{"success":true,"flag":"s3cur3d_m0b1l3_l0g1n_1ff8ddb7}"}
```

## Full Flag
Combining both parts:
```
picoCTF{1_l13d_4b0ut_b31ng_s3cur3d_m0b1l3_l0g1n_1ff8ddb7}
```

