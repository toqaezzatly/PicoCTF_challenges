# **Challenge Writeup: Even RSA**
**Category:** Cryptography  
**Platform:** picoCTF  
**Flag:** `picoCTF{tw0_1$_pr!m3de643ad5}`

---

### **1. The Problem**
The challenge provides three values via a remote server:
- **$N$ (Modulus):** A very large number used to "lock" the message.
- **$e$ (Public Exponent):** Usually `65537`, used for encryption.
- **$cyphertext$ (c):** The secret message, but scrambled into a giant number.

### **2. The Vulnerability (The "Even" Trick)**
In RSA, the number **$N$** is supposed to be the result of multiplying two **Prime Numbers** ($p$ and $q$). Prime numbers (like 3, 7, 13, etc.) are always odd. Therefore, a secure $N$ should always be an **odd number**.

**Our Observation:**  
The $N$ provided ended in `...978`. This means it is an **EVEN** number.
- Any even number is divisible by **2**.
- Since 2 is the only even prime number, it means one of the factors is **$p = 2$**.
- This makes finding the other factor simple: **$q = N / 2$**.

### **3. Key Terms Explained**
You asked about the meaning of $c$ and $d$. Here is the breakdown:

*   **$c$ (Ciphertext):**  
    This is the **locked box**. It is the original flag that has been mathematically scrambled. Without the private key, it just looks like a meaningless giant number.
*   **$d$ (Private Key / Exponent):**  
    This is the **physical key** that opens the box. In RSA, $d$ is calculated using $p$, $q$, and $e$. Once you have $d$, you can "un-scramble" $c$ to get the original text.
*   **$N$ (Modulus):**  
    This is the **size of the lock**. It defines the mathematical space where the encryption happens.

---

### **4. Step-by-Step Solution**

To solve this, we followed these steps in Python:

1.  **Factor $N$:** Since $N$ was even, we set $p = 2$ and $q = N // 2$.
2.  **Calculate Phi ($\phi$):** This is a required step to find the key.  
    $\phi = (p - 1) \times (q - 1)$
3.  **Calculate the Private Key ($d$):**  
    We used the `modinv` (modular inverse) of $e$ and $\phi$. This gave us the "key" to the encryption.
4.  **Decrypt the Message ($m$):**  
    We used the RSA decryption formula: $m = c^d \pmod N$.
5.  **Convert to Text:**  
    The resulting number $m$ was converted from "Long Integer" format back into readable "Bytes/ASCII."

### **5. Final Code Snippet**
```python
p = 2
q = n // 2
phi = (p - 1) * (q - 1)
d = pow(e, -1, phi) # Finding the private key
m = pow(c, d, n)    # Decrypting: (c^d) % n

print(long_to_bytes(m).decode())
# Output: picoCTF{tw0_1$_pr!m3de643ad5}
```
