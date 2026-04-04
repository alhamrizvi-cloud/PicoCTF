## picoCTF 2025 – Forensics: Event Viewing (Simple Writeup)

In this challenge, we had to investigate a malware infection using Windows Event Logs. The goal was to find hidden pieces of a flag by analyzing different system events.

---

## 🔍 Scenario

An employee reported strange behavior on their system:

* They installed a software using a downloaded installer
* When they opened it, nothing happened
* After that, every time they logged in, a black command prompt flashed and the system shut down

This clearly looked suspicious, so we analyzed the Windows Event Logs to understand what was happening.

---

## 🛠 Step 1: Reading the EVTX File

Windows logs are stored in `.evtx` format, which is not easy to read directly. So first, we converted it into XML using a Python script.

This allowed us to go through each event and search for useful information.

---

## 🧩 Phase 1: Software Installation (Event ID 11707)

We checked installation logs and found an event with **ID 11707**.

Inside it, there was a Base64 encoded string:

* Encoded: `cGljb0NURntFdjNudF92aTN3djNyXw==`
* Decoded: `picoCTF{Ev3nt_vi3wv3r_`

✅ This gave us the **first part of the flag**

---

## 🧩 Phase 2: Software Execution (Event ID 4657)

Next, we looked at **Security logs (Event ID 4657)**, which track registry changes.

We found a suspicious modification in the **Run key** (used for persistence).

Another Base64 string was hidden there:

* Encoded: `MXNfYV9wcjN0dHlfdXMzZnVsXw==`
* Decoded: `1s_a_pr3tty_us3ful_`

✅ This gave us the **second part of the flag**

---

## 🧩 Phase 3: Shutdown Behavior (Event ID 1074)

Finally, we analyzed **System logs (Event ID 1074)**, which record shutdown events.

We noticed something interesting:

* A normal process (`RuntimeBroker.exe`) triggered an initial shutdown
* Then a suspicious file (`custom_shutdown.exe`) forced the actual shutdown

This was clearly done to confuse analysis.

Inside this event, we found one more Base64 string:

* Encoded: `dDAwbF84MWJhM2ZlOX0=`
* Decoded: `t00l_81ba3fe9}`

✅ This gave us the **third part of the flag**

---

## 🏁 Final Flag

Now we combine all three parts:

```
picoCTF{Ev3nt_vi3wv3r_
1s_a_pr3tty_us3ful_
t00l_81ba3fe9}
```

👉 Final Answer:

```
picoCTF{Ev3nt_vi3wv3r_1s_a_pr3tty_us3ful_t00l_81ba3fe9}
```

---

## 💡 Key Takeaways

* Windows Event Logs are very useful in forensic analysis
* Malware often uses **registry persistence** to stay active
* Attackers may use **legitimate processes as a cover**
