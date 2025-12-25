# Challenge Write-up: Transformation (picoCTF)

## 1. Challenge Overview
The challenge provides a file named `enc` containing a string of what appears to be Chinese characters (e.g., `灩捯䍔䙻...`). It also provides the Python snippet used to generate this file:

```python
''.join([chr((ord(flag[i]) << 8) + ord(flag[i + 1])) for i in range(0, len(flag), 2)])
```

**The Goal:** Reverse this process to turn the 16-bit Unicode characters back into the original 8-bit ASCII characters (the flag).

---

## 2. Technical Analysis: How the Encoding Works
To solve this, we must first understand exactly what the Python code is doing.

### The Logic of "Packing"
Standard ASCII characters (like 'p', 'i', 'c', 'o') use **8 bits** of memory. However, Unicode characters can use **16 bits** (or more). The programmer decided to "pack" two 8-bit characters into one 16-bit slot.

**Example: Packing 'p' and 'i'**
1.  **`ord(flag[i])`**: Get the numerical value of the first character.
    *   'p' = 112 (Binary: `01110000`)
2.  **`<< 8` (Bitwise Left Shift)**: Move those bits 8 places to the left.
    *   `01110000` becomes `01110000 00000000`. This effectively moves 'p' to the **"High Byte"** position.
3.  **`+ ord(flag[i+1])`**: Add the value of the second character.
    *   'i' = 105 (Binary: `01101001`).
    *   Result: `01110000 01101001` (A 16-bit number).
4.  **`chr(...)`**: Convert that 16-bit number into a single character.
    *   `01110000 01101001` is 28777 in decimal, which is the Chinese character `灩`.

**Summary:** Every 1 character in the `enc` file represents 2 characters of the flag.

---

## 3. The Reverse Engineering Logic
To get the flag back, we perform the exact opposite steps for every character in the `enc` file.

### Step A: Extract the High Byte (The 1st character)
We need to move the bits 8 places back to the right to isolate the first character.
*   **Operation:** `val >> 8`
*   **Result:** `01110000 01101001` → `00000000 01110000` (which is 'p').

### Step B: Extract the Low Byte (The 2nd character)
We need to "delete" the first 8 bits and keep only the last 8 bits. We do this using a **Mask**.
*   **The Mask:** `0xFF` (Binary: `00000000 11111111`).
*   **Operation:** `val & 0xFF` (Bitwise AND).
*   **Result:** Only the bits that align with the "1"s in the mask are kept.
*   `01110000 01101001` AND `00000000 11111111` = `00000000 01101001` (which is 'i').

---

## 4. Step-by-Step Solution

### Step 1: Get the file
Download the encoded file from the picoCTF server:
```bash
wget https://mercury.picoctf.net/static/a757282979af14ab5ed74f0ed5e2ca95/enc
```

### Step 2: The Python Solution Script
Create a file named `solve.py` or run this directly in your terminal:

```python
# Open the encoded file
encoded_val = open("enc").read()

flag = ""

for char in encoded_val:
    # Get the 16-bit integer value of the character
    num = ord(char)
    
    # Extract first character (High Byte)
    first = chr(num >> 8)
    
    # Extract second character (Low Byte)
    second = chr(num & 0xFF)
    
    flag += first + second

print(flag)
```

### Step 3: Run the solver
```bash
python3 solve.py
```

**Alternative (One-Liner):**
If you prefer the command line, this single command does the same thing:
```bash
cat enc | python3 -c "import sys; enc = sys.stdin.read(); print(''.join([chr(ord(c) >> 8) + chr(ord(c) & 0xFF) for c in enc]))"
```

---

## 5. Summary of Key Concepts
*   **Bitwise Shifting (`<<`, `>>`):** Used to move bits to different "slots" (High byte vs Low byte).
*   **Endianness:** This challenge uses **Big-Endian** logic, meaning the first character of the pair is stored in the most significant (leftmost) position.
*   **Encoding vs. Encryption:** This wasn't true encryption, just a data transformation. By understanding the math, we could perfectly reverse the transformation.
*   **The 0xFF Mask:** A crucial tool in reverse engineering used to isolate specific bytes of data within a larger number.

**Flag Found:** `picoCTF{16_bits_inst34d_of_8_...}` (Final characters vary by instance).
