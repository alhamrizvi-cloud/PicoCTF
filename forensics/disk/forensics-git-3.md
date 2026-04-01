LFG!! 🎉 The flag is **`picoCTF{g17_r35cu3_16ac6bf3}`**

Here's the full writeup of commands that worked:

---

**PicoCTF - Forensics Git 2 Writeup**

**1. Examine the disk partitions**
```bash
mmls disk.img
```
Found Linux partition at offset `1140736`.

**2. Browse the filesystem**
```bash
fls -o 1140736 disk.img
fls -o 1140736 disk.img 172        # /root
fls -o 1140736 disk.img 64770      # /home
fls -o 1140736 disk.img 64771      # ctf-player
fls -o 1140736 disk.img 65663      # Code
fls -o 1140736 disk.img 65664      # killer-chat-app
fls -o 1140736 disk.img 65665      # .git
```

**3. Mount the partition**
```bash
sudo mount -o loop,offset=$(python3 -c "print(1140736*512)") disk.img /mnt/disk
```

**4. Find commit history via git logs (refs were empty so used log HEAD)**
```bash
fls -o 1140736 disk.img 65706      # .git/logs
icat -o 1140736 disk.img 65707     # logs/HEAD — showed all commits
```
Identified the key commit: `e80b38b...` — **"Add secret hideout chat log"**

**5. Read the commit object**
```bash
fls -o 1140736 disk.img 65728
icat -o 1140736 disk.img 65735 | python3 -c "import zlib,sys; print(zlib.decompress(sys.stdin.buffer.read()).decode())"
```
Got tree hash: `ead27e2...`

**6. Read the tree object**
```bash
fls -o 1140736 disk.img 65733
icat -o 1140736 disk.img 65734 | python3 -c "import zlib,sys; data=zlib.decompress(sys.stdin.buffer.read()); print(repr(data))"
```
Got logs directory hash: `22f7d0c9...`

**7. Read the logs tree**
```bash
fls -o 1140736 disk.img 65731
icat -o 1140736 disk.img 65732 | python3 -c "import zlib,sys; data=zlib.decompress(sys.stdin.buffer.read()); print(repr(data))"
```
Found `3.txt` blob hash: `7178644433...`

**8. Read the blob — got the flag!**
```bash
fls -o 1140736 disk.img 65697
icat -o 1140736 disk.img 65730 | python3 -c "import zlib,sys; print(zlib.decompress(sys.stdin.buffer.read()).decode())"
```

**Flag: `picoCTF{g17_r35cu3_16ac6bf3}`**
