# 🛠️ Corrupted File  

Let’s get to the point → check description + hints carefully.  

**Description**  
File looks broken… maybe just a couple of bytes make all the difference.  

**Hints**  
1. Check the header.  
2. JPEG.  
3. Use `xxd` or `hexdump`.  

---

## Step 1: Know JPEG rules  
- Must start with `FF D8`.  
- Must end with `FF D9`.  

Target is header, but I checked footer too.  

```
xxd file | head
```

![wrong header](https://github.com/user-attachments/assets/257676d8-882b-4b3b-95a8-8d033df39236)  
→ first 2 bytes are wrong.  

```
xxd file | tail
```

![footer check](https://github.com/user-attachments/assets/57d76f65-adab-40d1-b587-24b69e62cca0)  
→ `0a` is just newline, ignore.  

---

## Step 2: Dump + Edit  
| Part             | Meaning                                        |
| ---------------- | ---------------------------------------------- |
| `xxd`            | hex dump tool                                  |
| `./Desktop/file` | original binary (corrupted.jpg)                |
| `>`              | redirect output                                |
| `file.hex`       | save as text                                   |

🔥 Reads binary → outputs hex → saves to `file.hex`.  

```
vim file.hex
```

![vim edit](https://github.com/user-attachments/assets/bec2b2e8-72a6-4a78-9910-128797f27581)  

Press `i`, edit first 2 bytes `5c78` → `ffd8`.  
`esc` → `:wq`.  

---

## Step 3: Re‑render  
```
xxd -r file.hex > fixed.jpg
xdg-open fixed.jpg
```

![fixed image](https://github.com/user-attachments/assets/23ba0ec1-81e1-4e64-acd7-555b1849e823)

---

## Step 4: Extract text  
```
tesseract fixed.jpg fixed
cat fixed.txt
```

![tesseract output](https://github.com/user-attachments/assets/c47d573a-7f1f-4af7-93f9-c70eb1a4a0ee)

---

✅ That’s it. Header fixed → image restored → text extracted.  
