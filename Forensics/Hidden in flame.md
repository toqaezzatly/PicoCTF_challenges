# 🔥 Hidden In Flame  

In the provided `log.txt` file, you’ll notice it is base64 encoded.  
Hint says something about a generated image.  

```
base64 -d ./Desktop/logs.txt > output_image
```

---

After decoding → it’s a PNG.  
Open it directly, or with:  

```
xdg-open output_image
```

---

And here it is, the thing hidden in the flame.  

---

How to extract it? → **tesseract**.  
Well done, you’re doing very well.  

```
tesseract output_image output
```

This gives you `output.txt`.  

---

Clean the middle line (remove spaces).  
Then use `xxd -r -p`.  

| Part  | Meaning                                        |
| ----- | ---------------------------------------------- |
| `xxd` | Hex dump tool                                  |
| `-r`  | Reverse (hex → binary/text)                    |
| `-p`  | Plain hex mode (continuous hex)                |

---

Final step:  

```
echo "7069636F4354467B666F72656E736963735F616E616C797369735F69735F616D617A696E675F35646161346132667D" | xxd -r -p
```

---

✅ Output:  
```
picoCTF{forensics_analysis_is_amazing_5daa4a2f}
```

---

Straight chain: base64 → PNG → OCR → hex → flag. Hidden in the flame, revealed.  
