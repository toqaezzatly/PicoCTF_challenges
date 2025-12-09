# 💽 DISKO 1  

A simple explanation.  

---

## **`.dd` extension**  
- Raw disk image file.  
- Bit‑for‑bit copy of a drive/partition/storage device.  
- Created with `dd`.  

Example:  
```bash
dd if=/dev/sda of=disk.dd bs=4M
```

- `if=` input file (disk/device)  
- `of=` output file (image)  
- `bs=` block size  

Contains everything: filesystem, deleted files, boot sectors, unallocated space, hidden data.  
That’s why tools like `strings`, `mount`, `foremost`, `photorec`, `binwalk` are useful.  

---

## **`.gz` extension**  
- Compressed file using gzip.  
- Like `.zip` but single‑file only.  
- Saves space.  

Decompress:  
```bash
gunzip file.gz
```

Or keep original:  
```bash
gunzip -c file.gz > out
```

---

### In this challenge  
`disko-1.dd.gz`  
▼ decompress →  
`disko-1.dd`  
▼ raw disk dump  

---

### Summary  

| Extension | Meaning         | You can do                          |
| --------- | --------------- | ----------------------------------- |
| `.dd`     | disk image      | mount, carve, analyze, strings, etc |
| `.gz`     | gzip compressed | decompress with `gunzip` or `gzip -d` |

---

### Steps  
```bash
gunzip ./Desktop/disko-1.dd.gz
strings Desktop/disko-1.dd | grep -i picoctf
```

![strings output](https://github.com/user-attachments/assets/733cc09e-a833-421e-914e-85ee9d39bcb7)

---

That’s it. Decompress → analyze → flag.  

