# Challenge Write-up: RED (picoCTF)

## 1. Challenge Overview
*   **Category:** Forensics / Steganography
*   **Description:** RED, RED, RED, RED.
*   **File:** `red.png` (A solid red square image).
*   **Goal:** Discover and decode the hidden information within the image.

---

## 2. Phase 1: Hint Analysis & Metadata
The first step in any forensics challenge is analyzing hints and metadata.

### Hint Interpretation:
1.  **"Check whatever Facebook is called now":** Facebook’s parent company is **Meta**. This is a direct pointer to **Metadata** (hidden info about the file).
2.  **"Red? Ged? Bed? Aed?":** A wordplay emphasizing "Red" while suggesting that values might be altered or shifted.

### Metadata Extraction (`exiftool`):
Running `exiftool red.png` revealed a field called **Poem**:
> **C**rimson heart, vibrant and bold,.  
> **H**earts flutter at your sight..  
> **E**venings glow softly red,.  
> **C**herries burst with sweet life..  
> **K**isses linger with your warmth..  
> **L**ove deep as merlot..  
> **S**carlet leaves falling softly,.  
> **B**old in every stroke.

**The Secret Message:** By taking the first letter of every sentence (the acrostic), we get the message: **CHECK LSB**.

---

## 3. Phase 2: Technical Deep Dive (The LSB Logic)
You asked how this works "under the hood." Here is the technical breakdown of the **Least Significant Bit (LSB)** technique.

### The Anatomy of a Pixel
An RGB image is made of pixels. Each pixel usually consists of **24 bits**:
*   **Red Channel:** 8 bits (Values 0–255)
*   **Green Channel:** 8 bits (Values 0–255)
*   **Blue Channel:** 8 bits (Values 0–255)

In this challenge, the image is "Pure Red." A pure red pixel looks like this:
*   Red: `11111111` (255)
*   Green: `00000000` (0)
*   Blue: `00000000` (0)

### The "Invisible" Change
To hide data, the programmer changes the **very last bit** (the Least Significant Bit) of the Red channel.
*   **Normal Red:** `11111111` (255)
*   **Modified Red:** `11111110` (254)

To the human eye, the difference between brightness level 255 and 254 is impossible to see. However, to a computer, that `0` at the end is a piece of data. By lining up the LSBs of 8 pixels, the computer forms 1 byte (a character).

---

## 4. Phase 3: Tool Analysis (`strings` vs. `zsteg`)
We discussed why some tools work and others don't.

### Why `strings` failed:
The `strings` command looks for **plain text** bytes sitting together in a row (e.g., `112, 105, 99, 111` for "pico"). 
In LSB steganography, the "p" isn't a single byte; its 8 bits are scattered across 8 different pixels. `strings` cannot "reassemble" these shattered bits, so it sees only garbage or the metadata poem.

### Why `zsteg` worked:
`zsteg` (installed via Ruby's `gem`) is a specialized scanner. It doesn't look for words; it looks for **patterns in bits**. 
1.  It extracts every LSB from the image.
2.  It groups them into 8-bit blocks.
3.  It checks if those blocks match known data types (like Base64 or ASCII).

---

## 5. Phase 4: Execution & Decoding

### Step 1: Run zsteg
Using `zsteg -a red.png` revealed the following hidden data in the red channel:
`b1,rgba,lsb,xy .. text: "cGljb0NURntyM2RfMXNfdGgzX3VsdDFtNHQzX2N1cjNfZjByXzU0ZG4zNTVffQ=="`

### Step 2: Recognize the Encoding
The string ends with `==`, which is the "signature" of **Base64 encoding**. Base64 is used to turn binary data into printable text characters.

### Step 3: Decode the Flag
Using the terminal to decode the Base64 string:
```bash
echo "cGljb0NURntyM2RfMXNfdGgzX3VsdDFtNHQzX2N1cjNfZjByXzU0ZG4zNTVffQ==" | base64 -d
```

---

## 6. Final Summary
*   **Hint:** "Meta" (Facebook) led to Metadata.
*   **Acrostic:** The Poem's first letters spelled "CHECK LSB."
*   **Technique:** LSB hides bits in the 8th bit of color channels where humans can't see the change.
*   **Tool:** `zsteg` was used because `strings` cannot reassemble bits hidden inside pixel data.
*   **Flag:** `picoCTF{r3d_1s_th3_ult1m4t3_cur3_f0r_54dn355_}`
