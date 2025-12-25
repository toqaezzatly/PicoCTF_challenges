# Challenge Write-up: Ph4nt0m 1ntrud3r (picoCTF)

## 1. Challenge Overview
*   **Category:** Forensics
*   **Author:** Prince Niyonshuti N.
*   **Main File:** `myNetworkTraffic.pcap`
*   **Objective:** Analyze network traffic to find a hidden flag stolen by a "digital ghost."
*   **Hint:** "Time is essential."

---

## 2. Initial Forensic Investigation
Upon opening the provided `.pcap` file in Wireshark, we see a small number of TCP packets. By looking at the **TCP Payload** (the data inside the packet), we notice strings that look like **Base64** (characters like `A-Z, a-z, 0-9` ending in `==`).

### The Trap:
If you try to read the packets in the order they appear (Packet 1, then Packet 2, etc.), and decode them, the flag looks like this:
`_34sy_tbh_4r_e...picoCTF{...`
It is **scrambled** and makes no sense.

---

## 3. The "Aha!" Moment: Timing Analysis
The challenge description says the intruder moved in a "timely manner" and the hint says **"Time is essential."**

When we look at the **Time** column in Wireshark, we realize the intruder's trick:
*   The packets were sent out of order.
*   The packet that *should* be first actually has an earlier timestamp but appears later in the file.
*   The "Phantom Intruder" intentionally shuffled the packets to confuse standard analysis tools.

**The Solution:** We must sort the traffic by the **arrival time** of the frames before reading the data.

---

## 4. The Automated Solution (Forensic Pipeline)
To solve this efficiently, we use a command-line pipeline. This ensures perfect sorting and extraction.

### The Command:
```bash
tshark -r myNetworkTraffic.pcap -Y "tcp.len > 0" -T fields -e frame.time -e tcp.payload | sort | awk '{print $NF}' | xxd -p -r | base64 -d
```

### Breakdown of the Logic:
1.  **`tshark -r ... -e frame.time -e tcp.payload`**: We tell the tool to grab only two things: the **Time** the packet arrived and the **Data** (Payload) it carried.
2.  **`| sort`**: This is the most critical step. It re-arranges every line so that the packet with the earliest time comes first. This "un-shuffles" the intruder's work.
3.  **`| awk '{print $NF}'`**: Since each line now looks like `[Time] [Data]`, we use `awk` to throw away the time and keep only the last field (`$NF`), which is the data.
4.  **`| xxd -p -r`**: The data in PCAP files is stored in Hexadecimal. This command "reverses" the hex into actual Base64 characters.
5.  **`| base64 -d`**: Finally, we decode the combined Base64 string to reveal the plaintext flag.

---

## 5. Step-by-Step Decoding (Manual View)
Once the packets are sorted by time, the data chunks appear in this order:

1.  `cGljb0NURg==` $\rightarrow$ **picoCTF**
2.  `ezF0X3c0cw==` $\rightarrow$ **{1t_w4s**
3.  `bnRfdGg0dA==` $\rightarrow$ **nt_th4t**
4.  `XzM0c3lfdA==` $\rightarrow$ **_34sy_t**
5.  `YmhfNHJfZQ==` $\rightarrow$ **bh_4r_e**
6.  `NWU4Yzc4ZA==` $\rightarrow$ **5e8c78d**
7.  `fQ==` $\rightarrow$ **}**

---

## 6. Final Flag
**`picoCTF{1t_w4snt_th4t_34sy_tbh_4r_e5e8c78d}`**

---

## 7. Key Takeaways for Forensics
*   **Don't trust the Packet Number:** In real attacks and CTFs, hackers often transmit data out of order to bypass simple "Follow TCP Stream" filters.
*   **Metadata is King:** The timestamp (`frame.time`) is a piece of metadata that often reveals the true sequence of events.
*   **Pipeline Efficiency:** Using tools like `tshark` combined with `sort` and `awk` allows you to process thousands of packets in seconds, which would be impossible to do manually in Wireshark.
