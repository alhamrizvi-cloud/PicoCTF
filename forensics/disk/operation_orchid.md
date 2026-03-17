like the sleuthkt apprentice challenge before, i used mmls and found here are the dirs

<img width="1366" height="693" alt="image" src="https://github.com/user-attachments/assets/df5afa1e-a8a5-49ef-8d52-87bb54bc8ac1" />

<img width="1366" height="693" alt="image" src="https://github.com/user-attachments/assets/a230774a-5037-4a1a-b25b-ffab3b0e4210" />

i tried using icat at flag.txt but failed,

then i used icat -o 411648 disk.flag.img 1782 > flag.txt.enc

since we already have the password and based on his bash history he used this cmd


openssl aes256 -d -salt -in flag.txt.enc -out flag.txt -k unbreakablepassword1234567

we got the flag!
