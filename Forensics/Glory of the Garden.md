
# CTF Writeup: Glory of the Garden
*   **Event:** picoCTF
*   **Category:** Forensics
*   **Difficulty:** Easy
*   **Author:** jedavis/Danny

---

## 1. Challenge Description
The challenge provides a file named `garden.jpg`. The description states: *"This file contains more than it seems. Get the flag from garden.jpg."* 

A hint is provided suggesting the use of a **Hex Editor**.

## 2. Initial Analysis
When you open `garden.jpg`, it appears to be a normal photograph of a garden. There are no visual clues or hidden text written on the image itself. 

In forensics challenges, if the image looks normal, the information is usually hidden:
1.  In the metadata (EXIF data).
2.  Appended to the end of the file (after the image trailer).
3.  Embedded using steganography tools (like Steghide).

Given the hint about a **Hex Editor**, the most likely scenario is that the flag is stored as plain text inside the file's binary data.

---

## 3. Solution Steps

### Method 1: Using the Command Line (Fastest)
The `strings` command searches for printable character sequences in a binary file. Since we know the flag format for picoCTF starts with `picoCTF{`, we can pipe the output to `grep`.

**Command:**
```bash
strings garden.jpg | grep "picoCTF"
```

**Output:**
```text
Here is a flag "picoCTF{now_you_know_about_redundancy}"
```

---

### Method 2: Using a Hex Editor (Manual)
If you prefer to follow the hint literally:
1.  Open an online hex editor (like [HexEd.it](https://hexed.it/)) or a desktop tool like **HxD** or **Ghex**.
2.  Upload `garden.jpg`.
3.  Scroll to the very bottom of the file.
4.  **Observation:** Every JPEG file must end with the hex signature `FF D9` (the "End of Image" marker). 
5.  Looking at the data **after** `FF D9`, you will see the following text in the ASCII column:
    `Here is a flag "picoCTF{now_you_know_about_redundancy}"`

---

## 4. Why This Works
The JPEG file format has a specific structure. It starts with a header (`FF D8`) and ends with a trailer (`FF D9`). Image viewers are programmed to stop reading the file once they hit the `FF D9` marker. 

By "appending" (adding) text after that marker, the image remains viewable and uncorrupted, but the extra data remains hidden from standard viewers. This is a basic form of data hiding.

## 5. Flag
**`picoCTF{now_you_know_about_redundancy}`**
