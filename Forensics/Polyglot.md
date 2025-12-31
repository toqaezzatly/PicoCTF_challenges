# Writeup: Secret of the Polyglot
**Category:** Forensics
**Author:** Syreal
**Challenge:** Secret of the Polyglot

### The Objective
We are given a file named `flag2of2-final.pdf`. The challenge description hints that the "Network Operations Center" is getting conflicting information about the file type. We need to extract data from this file to assemble the flag.

### Step-by-Step Solution

#### 1. Initial Inspection (Part 2 of the Flag)
The file has a `.pdf` extension. Naturally, the first step is to try opening it with a standard PDF viewer (like Adobe Reader, Chrome, or Firefox).

*   **Action:** Open `flag2of2-final.pdf`.
*   **Result:** The PDF opens successfully. It displays a single line of text:
    > `_&_pdf_2a6c1e11}`
    *(Note: The last 8 characters may vary depending on your user instance).*

This looks like the **second half** of the flag. We are missing the beginning.

#### 2. Forensics Analysis
To understand why the challenge description mentions "conflicting information," we can use the terminal command `file` to check the file's "Magic Bytes" (the file signature).

*   **Command:**
    ```bash
    file flag2of2-final.pdf
    ```
*   **Output:**
    ```text
    flag2of2-final.pdf: PNG image data, 50 x 50, 8-bit/color RGBA, non-interlaced
    ```

**The Twist:** Even though the extension is `.pdf`, the operating system identifies it as a **PNG image**. This is because the file starts with the hexadecimal bytes `89 50 4E 47` (the signature for PNGs).

#### 3. Visual Inspection (Part 1 of the Flag)
Since the system thinks it is an image, let's treat it like one.

*   **Action:** Rename the file to change the extension from `.pdf` to `.png`.
    ```bash
    mv flag2of2-final.pdf flag2of2-final.png
    ```
*   **Action:** Open the file in an image viewer.
*   **Result:** The image displays the text:
    > `picoCTF{f1u3n7_1n_pn9`

This is the **first half** of the flag.

#### 4. Assemble the Flag
Now we simply concatenate the text found in the image with the text found in the PDF.

*   **Image:** `picoCTF{f1u3n7_1n_pn9`
*   **PDF:** `_&_pdf_2a6c1e11}`

**Final Flag:**
```text
picoCTF{f1u3n7_1n_pn9_&_pdf_2a6c1e11}
```

---

### Technical Explanation: How does this work?
This file is a **Polyglot**. A polyglot is a file that is valid in multiple formats simultaneously.

1.  **The PNG Part:** A PNG file is defined by a specific header at the very beginning of the file. Image viewers read the start of the file, see the PNG header, render the image data, and ignore any "junk" data appended at the end.
2.  **The PDF Part:** PDF readers are more forgiving. They often scan the file looking for the `%PDF` signature and the xref table (usually near the end of the file) to figure out how to display the document. They happily ignore the "junk" (the PNG data) at the beginning.

By gluing a PDF onto the end of a PNG, you create a file that works in both viewers.

---

### Real-World Similar Thing: "GIFAR" and Upload Bypasses

In the real world, polyglots are not just used for CTF puzzles; they are a known vector for security vulnerabilities, often referred to as **Content-Type Confusion attacks**.

#### The "GIFAR" Exploit
A famous example of this is the **GIFAR** (GIF + JAR) attack.
*   **The Setup:** An attacker creates a file that is valid as both a **GIF image** and a **Java JAR executable**.
*   **The Attack:** The attacker uploads this file to a website that allows image uploads (like a forum or social media profile). The website scans the file, sees the GIF header, verifies it is a "safe image," and allows the upload.
*   **The Execution:** The attacker then tricks the victim's browser into loading that same file as a Java applet. Since the file contains valid Java bytecode (hidden inside the "image"), the browser executes it, allowing the attacker to steal cookies or perform Cross-Site Scripting (XSS).

#### Evasion of Security Filters
Hackers also use polyglots to bypass **Data Loss Prevention (DLP)** systems or Antivirus scanners.
*   If a company blocks `.exe` files but allows `.pdf` files, an attacker might embed a malicious executable script inside a valid PDF structure.
*   A naive scanner checks the file, sees it opens as a PDF, and lets it through the firewall.
*   Once inside the network, the attacker uses a script or tool to extract the hidden payload from the PDF.
