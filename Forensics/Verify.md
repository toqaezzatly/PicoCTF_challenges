### Writeup for "Verify"
Here is a clean, step-by-step writeup you can use to document how you solved the challenge.

**Challenge:** Verify
**Category:** Forensics / Bash
**Objective:** Identify a legitimate file among fake ones using a checksum, then decrypt it to retrieve the flag.

**Solution:**

1.  **Retrieve the Target Checksum:**
    First, we check the content of `checksum.txt` to see the SHA-256 hash we are looking for.
    ```bash
    cat checksum.txt
    # Output: 467a10447deb3d4e17634cacc2a68ba6c2bb62a6637dad9145ea673bf0be5e02
    ```

2.  **Find the Matching File:**
    There are many files in the `files/` directory. We calculate the SHA-256 hash for all of them and use `grep` to filter for the hash we found in the previous step.
    ```bash
    sha256sum files/* | grep "467a10447deb3d4e17634cacc2a68ba6c2bb62a6637dad9145ea673bf0be5e02"
    ```
    **Result:** The command identifies that `files/c6c8b911` is the matching file.

3.  **Decrypt the Flag:**
    Now that we have the correct file, we use the provided decryption script.
    ```bash
    ./decrypt.sh files/c6c8b911
    ```

**Flag:**
`picoCTF{trust_but_verify_c6c8b911}`

