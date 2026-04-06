## Phase 1: Checking the Network Traffic
The first step was looking at the `try_me.pcap` file (a recording of internet activity). 

* **The Problem:** Most of the traffic was encrypted (HTTPS), meaning we couldn't see what was inside.
* **The Clue:** There were a few unencrypted (HTTP) requests. By filtering the traffic, the researchers found two image files being downloaded: `duck.png` and `evil_duck.png`.
* **The Suspicion:** `evil_duck.png` was a much larger file than it should have been for its quality. This is a huge "red flag" that data is hidden inside the image.

### Phase 2: Finding the Hidden Method
Standard tools didn't work to "unhide" the data, so the researchers looked at the user's web history within the capture file to see what they were researching.
* They saw the user visited **GitHub** and **PowerShell.org**.
* By searching for "PowerShell steganography," they found a tool called **Invoke-PSImage**. 
* This tool hides PowerShell code inside the pixels of a picture.

### Phase 3: Extracting the Code
Using a decoder specifically for this PowerShell method, they scanned `evil_duck.png`. Instead of a flag, they found a small **PowerShell script**:

```powershell
$string1 = "HEYWherE(IS_tNE)50uP?^DId_YOu(]E@t*mY_3RD()B2g3l?"
$string2 = "8,:8+14>Fx0l+$*KjVD>[o*.;+1|*[n&2G^201l&,Mv+_'T_B"
# The script then XORs (mixes) these two strings together...
```

### Phase 4: Getting the Flag
The script found inside the image was a "key." It didn't show the flag directly; it showed two scrambled strings. 
1.  The researchers ran that script in PowerShell.
2.  The script performed a math operation (XOR) to combine `$string1` and `$string2`.
3.  The result was saved into a file called `flag.txt`.

**The Final Flag:**
> `picoCTF{n1c3_job_f1nd1ng_th3_s3cr3t_in_the_im@g3}`

---

### Summary in 3 Bullets:
* **The Download:** The user downloaded a suspicious "Evil Duck" image.
* **The Hidden Script:** A PowerShell script was hidden in the image's pixels.
* **The Calculation:** Running that script decoded the final text.
