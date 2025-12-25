# Writeup: PicoCTF - Flag Hunters

## 1. Challenge Information
*   **Author:** syreal
*   **Category:** Binary Exploitation / Logic
*   **Difficulty:** Easy/Medium
*   **Objective:** Manipulate the program's lyric interpreter to jump to the start of the file (index 0) where the hidden flag is stored.

## 2. Analysis
The challenge provides a Python script that acts as a custom "lyric interpreter." Here is the breakdown of how the program works:

1.  **Memory Layout:** The song is stored in a list called `song_lines`. The flag is located at the very beginning of this list (index 0) inside the `secret_intro` variable.
2.  **Execution Flow:** The program doesn't start at the beginning. It calls `reader(song_flag_hunters, '[VERSE1]')`, skipping the intro and the flag.
3.  **The Instruction Pointer (`lip`):** The variable `lip` tracks which line is currently being printed.
4.  **Vulnerable Input:** Inside the loop, the program looks for a line starting with `CROWD`. It then takes user input:
    ```python
    crowd = input('Crowd: ')
    song_lines[lip] = 'Crowd: ' + crowd
    ```
    This is a **Self-Modifying Code** vulnerability. Your input actually overwrites the "code" (the lyrics) stored in memory for that specific line.
5.  **Command Injection:** The interpreter splits lines by a semicolon: `for line in song_lines[lip].split(';'):`. It also supports a command called `RETURN [number]`, which manually sets the `lip` to that number.

## 3. The Strategy
Since we can overwrite a line in the song and include a semicolon to add a second command, we can inject a `RETURN` command. 

We want to go to **Line 0** because that is where the `secret_intro` (and the flag) starts.

*   **Step 1:** Wait for the program to reach the `Crowd: ` prompt.
*   **Step 2:** Provide an input that includes the `RETURN` command: `;RETURN 0`.
*   **Step 3:** The first time you enter this, the program is already middle-way through processing the current line, so it won't jump immediately. However, it **saves** your input into the song's memory.
*   **Step 4:** The next time the song hits the `REFRAIN` (where the `CROWD` line is), it reads your modified line: `Crowd: ;RETURN 0`.
*   **Step 5:** The interpreter splits the line at the `;`. It sees `RETURN 0`, sets the `lip` to 0, and begins printing the song from the very top, revealing the flag.

## 4. Exploitation (Step-by-Step)
1.  Connect to the server:
    `nc verbal-sleep.picoctf.net 64965`
2.  Let the lyrics play until you see the first prompt:
    `Crowd: `
3.  Type the following and press Enter:
    `;RETURN 0`
4.  The song will continue. Wait for it to finish the verse and repeat the refrain.
5.  When it reaches the modified line, it will trigger the jump.
6.  The flag will be printed as part of the "Secret Intro":
    `The ether’s ours to conquer, picoCTF{70637h3r_f0r3v3r_a5202532}`

## 5. Conclusion
The vulnerability exists because the program allows user input to modify its internal "instruction" list (`song_lines`) and provides a command (`RETURN`) that can manipulate the execution flow. By combining these, we performed a simple **Logic Injection** to access restricted memory areas (Line 0).

**Flag:** `picoCTF{70637h3r_f0r3v3r_a5202532}`
