
In this challenge, we were given a **.pptm file**. After checking many slides for the flag, I couldn't find anything. So I decided to inspect the file using the **strings** command and noticed some content suggesting that the file might actually contain a **ZIP archive**.

To confirm this, I used **Binwalk** to analyze the file, and it showed that there was indeed a ZIP archive embedded inside it. I then extracted it.

After spending a lot of time searching, I realized that the **grep** command wasn't able to find the flag because it was **not in a readable format**.

While exploring the extracted files, I navigated to:

```
./ppt/slideMasters/hidden
```

Inside this file I found the following text:

```
Z m x h Z z o g c G l j b 0 N U R n t E M W R f d V 9 r b j B 3 X 3 B w d H N f c l 9 6 M X A 1 f Q
```

This did not look like any common substitution cipher. However, it looked similar to **Base64 encoding**, so I decided to try decoding it.

First, I wrote a small script to remove the spaces:

```python
s = "Z m x h Z z o g c G l j b 0 N U R n t E M W R f d V 9 r b j B 3 X 3 B w d H N f c l 9 6 M X A 1 f Q"
s = s.split(" ")
print("".join(s))
```

Output:

```
ZmxhZzogcGljb0NURntEMWRfdV9rbjB3X3BwdHNfcl96MXA1fQ
```
