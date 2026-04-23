alhamrizvi@alhams-fedora:~/Downloads$ python3
Python 3.14.3 (main, Mar 26 2026, 00:00:00) [GCC 15.2.1 20260123 (Red Hat 15.2.1-7)] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> enc = open("level5.flag.txt.enc", "rb").read()
>>> known = b"picoCTF{"
... 
>>> key = bytes([a ^ b for a, b in zip(enc, known)])
... 
>>> print(key)
... 
b'7e5f7e5f'
>>> decrypted = bytes([c ^ key[i % len(key)] for i, c in enumerate(enc)])
>>> print(decrypted.decode())
... 
picoCTF{h45h_sl1ng1ng_40f26f81}
>>> 
