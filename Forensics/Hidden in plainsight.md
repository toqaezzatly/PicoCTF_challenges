# 🔎 Hidden in Plainsight  

Quite an interesting address… you can open the image, but you cannot actually see what it’s really carrying inside.  

---

## Step 1: Start with `exiftool`  
Pull the metadata, and you’ll see something unusual.  

![exiftool output](https://github.com/user-attachments/assets/2024b85b-5413-42d7-b346-fd46ac57bc73)

---

## Step 2: Base64 decoding  
Tried `base64 --decode` once, but it seems I have to do it again.  
But wait—**slow down**—there’s a hint.  

---

## Step 3: The hint → `steghide`  
The clue points straight at the tool name.  
So: re‑decode the text after “steghide” and then use the tool.  

---

## What is Steghide?  
- Hide information inside a media file (images, audio).  
- Extract that information later (with the right password).  
- To anyone else, the file looks normal—but you know there’s something inside.  

---

## Step 4: Extraction  
Run `steghide` with the decoded clue, and the hidden content comes out.  

![steghide output](https://github.com/user-attachments/assets/6dbce64f-cd52-439a-8463-d900f8cfd37c)

---

## ✅ And that’s it  
The secret was there all along, tucked neatly inside the image.  
