On **Ubuntu**, configuring **FFmpeg** is simpler since it can be installed directly from the package manager. Here’s how you can set it up:

---

### **1. Install FFmpeg**
Open a terminal and run:
```bash
sudo apt update && sudo apt install ffmpeg -y
```

---

### **2. Verify Installation**
Check if FFmpeg is installed and working:
```bash
ffmpeg -version
```
You should see output similar to:
```
ffmpeg version 4.x.x Copyright (c) 2000-2024...
```

---

### **3. Check FFmpeg Location**
Run:
```bash
which ffmpeg
```
It should return something like:
```
/usr/bin/ffmpeg
```
This means FFmpeg is installed and accessible system-wide. **You don’t need to manually set environment variables like in Windows**.

---

### **4. (Optional) Install Latest Version**
Ubuntu’s default package might not be the latest version. If you need the **latest FFmpeg**, install it from an external PPA:
```bash
sudo add-apt-repository ppa:savoury1/ffmpeg4 -y
sudo apt update
sudo apt install ffmpeg -y
```
Check the version again:
```bash
ffmpeg -version
```

---

### **5. (Optional) Add FFmpeg to PATH Manually**
If you installed FFmpeg manually (e.g., by compiling from source), you can add it to your `PATH` like this:

1. Open the `.bashrc` file:
   ```bash
   nano ~/.bashrc
   ```
2. Add this line at the end:
   ```bash
   export PATH=$PATH:/usr/local/bin/ffmpeg
   ```
3. Save and exit (`Ctrl + X`, then `Y`, then `Enter`).
4. Apply the changes:
   ```bash
   source ~/.bashrc
   ```
5. Verify by running:
   ```bash
   echo $PATH
   ```

---
