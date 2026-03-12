

## Simple Solution Explanation

The hint says the challenge is related to **how images from the moon landing were sent to Earth**.
If you search about this, you will find a technology called **Slow‑Scan Television**.

SSTV sends **images through audio signals**.
So the file `message.wav` is actually **an image hidden inside sound**.

To convert the sound into an image, we use a tool called **QSSTV** in Kali Linux.

---

# Steps

### 1. Install QSSTV

Install the tool in Kali Linux.

```bash
sudo apt-get install qsstv
```

---

### 2. Create a virtual audio cable

This lets the system send the sound file directly into QSSTV.

```bash
pactl load-module module-null-sink sink_name=virtual-cable
```

---

### 3. Open audio settings

Run:

```bash
pavucontrol
```

Check that **“Null Output”** device appears.

---

### 4. Start QSSTV

Run:

```bash
qsstv
```

Then:

* Go to **Options → Configuration → Sound**
* Select **PulseAudio**

---

### 5. Set recording source

In **pavucontrol → Recording tab**, set QSSTV to record from **Null Output**.

---

### 6. Choose correct SSTV mode

The challenge hint asks:
**“What is the CMU mascot?”**

The mascot of **Carnegie Mellon University** is **Scotty the Scottie Dog**.

So in QSSTV:

* Mode = **Scottie 1**
* Enable **Auto Slant**

---

### 7. Play the audio file

Now play the file:

```bash
paplay -d virtual-cable message.wav
```

QSSTV will **decode the sound and slowly create an image**.

The image shows the **flag**.

---

# Flag

```
picoCTF{beep_boop_im_in_space}
```

---

✅ **Idea of the challenge:**
Audio → SSTV signal → Decode with QSSTV → Image → Flag.
