First, we received a **PCAP file**. I opened it using **Wireshark** to analyze the network traffic.

I first searched for **UDP packets** and noticed that many packets were coming from the same IP address. So I applied this filter:

```
ip.src == 10.0.0.66
```

This showed all packets sent from that IP.

After playing around with different filters in Wireshark, I noticed that most of the UDP traffic had **source port 5000** and **destination ports 8990 or 8888**. But around **packet number 1104**, the source ports suddenly started looking random.

One of the packets had **source port 5112**. The number **112 is important because in ASCII it represents the letter "p"**, which is the starting letter of many **picoCTF flags** (`picoCTF{...}`). This suggested that the **port numbers might contain hidden ASCII data**.

To extract the numbers from the PCAP file, I used **tcpdump** with this command:

```
tcpdump -nr capture.pcap "udp and port 22" | awk {'print $3'} | cut -d . -f 5 | tr '\n' ' '
```

Then I cleaned the output to remove the extra digits:

```
tcpdump -nr capture.pcap "udp and port 22" | awk {'print $3'} | cut -d . -f 5 | sed 's/^5//g' | sed 's/^0//g' | tr '\n' ' '
```

This produced the following numbers:

```
112 105 99 111 67 84 70 123 112 49 76 76 102 51 114 51 100 95 100 97 116 97 95 118 49 97 95 115 116 51 103 48 125
```

These numbers represent **ASCII character codes**.
So I copied them into **CyberChef** and used the **“From Charcode”** operation.

After decoding, it revealed the **flag**.

Thanks for this guy's writup!
https://dmfrsecurity.com/2021/09/05/picoctf-2019-shark-on-wire-2-writeup/
