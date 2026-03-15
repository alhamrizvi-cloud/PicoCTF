just use binwalk -e to extract all the files again and again , you will get flag in flag4.xz which will be in hex format!

and run

alhamrizvi@alhams-fedora:~/temp$ echo "7069636f4354467b66316c656e406d335f6d406e3170756c407431306e5f6630725f3062326375723137795f33633739633562617d0a" | xxd -r -p
picoCTF{f1len@m3_m@n1pul@t10n_f0r_0b2cur17y_3c79c5ba}


