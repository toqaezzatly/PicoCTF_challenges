# Challenge Write-up: vault-door-training (picoCTF)

## 1. Challenge Overview
*   **Category:** Reverse Engineering
*   **Author:** Mark E. Haase
*   **Description:** As a training exercise, we are provided with the source code for a vault's digital lock. We need to analyze the code to find the password that opens the door.
*   **Files Provided:** `VaultDoorTraining.java`

---

## 2. Static Analysis of the Code
Unlike "Transformation," which required mathematical decoding, this challenge is a test of **Static Analysis**—the process of examining code without necessarily running it.

### The `main` Method
Looking at the `main` method, we see how the program handles our input:
```java
String input = userInput.substring("picoCTF{".length(), userInput.length()-1);
if (vaultDoor.checkPassword(input)) {
    System.out.println("Access granted.");
}
```
*   **The Logic:** The program takes your input (e.g., `picoCTF{password}`) and strips away the prefix (`picoCTF{`) and the suffix (`}`). 
*   **The Result:** Only the core password inside the brackets is passed to the `checkPassword` function.

### The `checkPassword` Method
This is where the secret is stored. The code is very straightforward:
```java
public boolean checkPassword(String password) {
    return password.equals("w4rm1ng_Up_w1tH_jAv4_000AXPNPN0i");
}
```
*   **`password.equals(...)`**: This is a direct string comparison. 
*   **Hardcoded Secret**: The password is "hardcoded" (written directly into the source code). The program simply checks if your input matches the string `"w4rm1ng_Up_w1tH_jAv4_000AXPNPN0i"`.

---

## 3. The Solution Step-by-Step

1.  **Read the Source:** Open the `VaultDoorTraining.java` file in any text editor (Notepad, VS Code, or even via `cat` in the terminal).
2.  **Locate the Comparison:** Find the `checkPassword` method.
3.  **Extract the String:** Copy the text inside the quotation marks: `w4rm1ng_Up_w1tH_jAv4_000AXPNPN0i`.
4.  **Format the Flag:** Wrap that string in the standard picoCTF format: `picoCTF{...}`.

---

## 4. Final Flag
`picoCTF{w4rm1ng_Up_w1tH_jAv4_000AXPNPN0i}`

---

## 5. Key Lessons Learned
*   **Hardcoded Credentials:** In real-world security, "hardcoding" passwords or API keys in source code is a major vulnerability. If an attacker can get the binary or the source, they can find the secret immediately.
*   **Static vs. Dynamic Analysis:** This challenge was solved via **Static Analysis** (reading code). If the code were obfuscated (made unreadable), we might have used **Dynamic Analysis** (running the code in a debugger to see what it compares the input to in real-time).
*   **Java String Handling:** The `.equals()` method is used for comparing the content of strings in Java, whereas `==` would compare the memory addresses. In CTFs, always look for `.equals()` or `strcmp()` in C/C++ to find password comparisons.
