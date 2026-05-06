# 🔐 Cybersecurity Lab Manual — Step-by-Step Usage Guide

> **Note:** All software is pre-installed on college systems.
> This guide tells you exactly where to click, what to type, and what to expect on screen — step by step.

---

## 📋 Table of Contents

| # | Experiment |
|---|-----------|
| 15 | [Analysing Android App Permissions & Mobile Traffic](#experiment-15-analysing-android-app-permissions-and-mobile-traffic) |
| 16 | [Testing IoT Device Security — Default Passwords & Open Ports](#experiment-16-testing-iot-device-security) |
| 17 | [Creating and Analyzing Disk Images Using dc3dd and Autopsy](#experiment-17-disk-image-creation-and-analysis) |
| 18 | [Log File Analysis for Incident Detection](#experiment-18-log-file-analysis-for-incident-detection) |
| 20 | [Privacy Audit of WhatsApp & Facebook + Data Breach Case Study](#experiment-20-privacy-audit-of-whatsapp--facebook) |
| 21 | [Conducting a Security Audit and Risk Assessment on Windows](#experiment-21-security-audit-and-risk-assessment-on-windows) |

---

---

# Experiment 15: Analysing Android App Permissions and Mobile Traffic

**Aim:** Intercept and analyse Android app HTTP and HTTPS traffic using Android Emulator and Burp Suite.

---

## 🔷 How to Open Android Studio

1. Look at your **Windows taskbar** at the bottom of the screen (or search in Start menu).
2. Click the **Start button** (Windows logo, bottom-left).
3. Type `Android Studio` and press **Enter**.
4. Android Studio opens. You will see a welcome screen with options like **"New Project"**, **"Open"**, etc.

---

## Step 1 — Open AVD Manager (Virtual Device Manager)

1. On the Android Studio welcome screen, look at the **top menu bar**.
2. Click **"Tools"** (in the menu bar at the very top).
3. A dropdown appears — click **"Device Manager"**.
4. A panel opens on the **right side** of the screen showing your virtual devices.
5. You will see a device listed (e.g., `Pixel 9` or `Medium Phone API 36`).

> If you see no device listed, ask your lab instructor — devices should already be created.

---

## Step 2 — Start the Emulator from Command Prompt

> We start the emulator using Command Prompt so it connects through Burp Suite automatically.

1. Press `Windows key + R` on your keyboard simultaneously.
2. A small **"Run"** box appears at the bottom-left of your screen.
3. Type `cmd` and press **Enter**.
4. A black **Command Prompt** window opens.
5. Type the following command exactly and press **Enter**:
   ```
   cd C:\Users\Admin\AppData\Local\Android\Sdk\emulator
   ```
   *(If your username is not Admin, replace Admin with your Windows username)*

6. Now type this and press **Enter** to see available virtual devices:
   ```
   emulator -list-avds
   ```
   You will see a list printed on screen like:
   ```
   Medium_Phone_API_36.1
   Pixel_2
   Pixel_9
   ```

7. Now type this command to **start the Pixel 9 emulator with Burp Suite proxy** and press **Enter**:
   ```
   emulator -avd Pixel_9 -http-proxy http://10.0.2.2:8080
   ```
8. Wait **2–3 minutes**. A phone screen will appear as a separate window — that is your Android emulator.
9. Wait until the Android home screen fully loads (you'll see app icons).

---

## Step 3 — Set Proxy Inside the Android Emulator

> We need to tell the emulator to send all traffic through Burp Suite.

1. In the emulator window, click the **upward arrow** at the bottom of the phone screen to open the app drawer (all apps).
2. Find and click the **"Settings"** app (it looks like a gear icon ⚙️).
3. Inside Settings, scroll down and click **"Network & Internet"**.
4. Click **"Internet"**.
5. You will see **"AndroidWifi"** with a gear/settings icon next to it — click that **gear icon**.
6. Look at the **top-right corner** of the screen — you will see a **pencil icon (✏️)** — click it.
7. Scroll down on that screen — click **"Advanced options"** to expand it.
8. Find the word **"Proxy"** — it says **"None"** next to it — click it.
9. A small menu appears with options: `None`, `Manual`, `Auto-config` — click **"Manual"**.
10. Two text boxes appear:
    - In **"Proxy hostname"** box — click it and type: `10.0.2.2`
    - In **"Proxy port"** box — click it and type: `8080`
11. Click the **"Save"** button at the bottom.

### ⚠️ If Save button is not responding — use this alternative method:

1. Open a **second Command Prompt window** (press `Win + R` → type `cmd` → Enter).
2. Type and press Enter:
   ```
   cd C:\Users\Admin\AppData\Local\Android\Sdk\platform-tools
   ```
3. Check the emulator is connected — type and press Enter:
   ```
   adb devices
   ```
   You should see: `emulator-5554    device`

4. Set the proxy by typing and pressing Enter:
   ```
   adb shell settings put global http_proxy 10.0.2.2:8080
   ```
5. Verify it worked — type and press Enter:
   ```
   adb shell settings get global http_proxy
   ```
   It should print: `10.0.2.2:8080`

---

## Step 4 — Open and Configure Burp Suite

### How to Open Burp Suite:

1. Click the **Start button** (Windows logo).
2. Type `Burp Suite` and press **Enter**.
3. Burp Suite opens. A dialog asks **"Select a project"**.
4. Click **"Temporary project"** (the first option).
5. Click the **"Next"** button (bottom-right).
6. Next screen asks about configuration — click **"Use Burp defaults"**.
7. Click **"Start Burp"** button.
8. Burp Suite is now open. You see the main window with tabs: **Dashboard**, **Target**, **Proxy**, **Intruder**, **Repeater**, etc.

### Configure Burp Suite to Accept Emulator Traffic:

1. Click the **"Proxy"** tab (top menu row of Burp Suite).
2. Click **"Proxy settings"** (appears as a sub-option or button within Proxy tab).
3. A Settings window opens. On the left panel, click **"Proxy"** under the Tools section.
4. On the right side, under **"Proxy Listeners"**, you see a row with `127.0.0.1:8080`.
5. Click that row to **select/highlight it**.
6. Click the **"Edit"** button (below the list).
7. A dialog box opens titled **"Edit proxy listener"**.
8. Under **"Bind to address"** section, you see three radio buttons:
   - `Loopback only`
   - `All interfaces`  ← **click this one**
   - `Specific address`
9. Select **"All interfaces"**.
10. Click **"OK"**.
11. Close the Settings window by clicking **"X"** or pressing Escape.

### Turn on Intercept:

1. You are now in the **Proxy** tab.
2. Click the **"Intercept"** sub-tab (just below the main tabs row).
3. You will see a button that says either **"Intercept is off"** or **"Intercept is on"**.
4. If it says **"Intercept is off"** — click it once. It will now say **"Intercept is on"**.

> ✅ Burp Suite is now ready and listening for traffic.

---

## Step 5 — Test HTTP Traffic Capture

1. In the **Android Emulator** window, click the **Chrome browser icon** on the home screen.
2. Click the **address bar** at the top of Chrome.
3. Type `http://example.com` and press **Enter** on your keyboard.
4. Switch back to **Burp Suite** on your PC.
5. Click the **"Proxy"** tab → click the **"HTTP history"** sub-tab.
6. You will see rows appearing — each row is a captured HTTP request. ✅

---

## Step 6 — Export Burp Suite CA Certificate (for HTTPS Interception)

1. In Burp Suite, click the **"Proxy"** tab.
2. Click **"Proxy settings"**.
3. Scroll down in the settings window until you see **"Import / Export CA certificate"** — click it.
4. A dialog opens. Select the radio button next to **"Certificate in DER format"** under the Export section.
5. Click **"Next"**.
6. Click **"Select file"**.
7. A file browser opens — navigate to your **Downloads** folder (look in the left panel for "Downloads").
8. In the **"File name"** field at the bottom, type: `burpcer.der`
9. Click **"Save"**.
10. Click **"Next"** → then **"Close"**.

---

## Step 7 — Send Certificate to Emulator and Install It

### Send the file to emulator:

1. Go to your **Command Prompt** window (the platform-tools one from Step 2 alternative).
2. Type and press Enter:
   ```
   adb push C:\Users\Admin\Downloads\burpcer.der /sdcard/Download/
   ```
3. You will see: `1 file pushed` — this means it worked.

### Install certificate inside the emulator:

1. In the **emulator**, open **Settings** ⚙️.
2. Scroll down and click **"Security & privacy"**.
3. Click **"More security & privacy"**.
4. Click **"Encryption & credentials"**.
5. Click **"Install a certificate"**.
6. Click **"CA certificate"**.
7. A red warning screen appears — click **"Install Anyway"**.
8. A file picker opens. Navigate to the **Downloads** folder.
9. Click on **"burpcer.der"**.
10. A confirmation message appears — the certificate is installed. ✅

---

## Step 8 — Verify HTTPS Interception

1. In the **emulator**, open **Chrome**.
2. Type `https://example.com` in the address bar and press Enter.
3. The page loads without any certificate error.
4. Go to **Burp Suite** → **Proxy** → **HTTP history**.
5. You now see HTTPS requests listed too — Burp Suite is intercepting encrypted traffic. ✅

---

## 5. Result

Android application network traffic was successfully intercepted using Burp Suite by configuring proxy settings in the Android emulator and installing the Burp Suite CA certificate. Both HTTP and HTTPS traffic is now visible.

---

## 6. Precautions

- Make sure Burp Suite is open and running **before** you start the emulator.
- Always set **"All interfaces"** in Burp Suite — without this the emulator cannot connect.
- After the experiment, turn **"Intercept"** OFF in Burp Suite so traffic flows normally.
- Do not close the emulator Command Prompt window while the emulator is running.

---

---

# Experiment 16: Testing IoT Device Security

**Aim:** Identify open ports, running services, weak credentials, and unencrypted communication in a simulated IoT device.

---

## 🔷 How to Open Windows Command Prompt

1. Press `Windows key + R` → type `cmd` → press **Enter**.

## 🔷 How to Open Kali Linux

1. Open **VirtualBox** — double-click the VirtualBox icon on the desktop.
2. In VirtualBox, you will see **"Kali Linux"** listed on the left side.
3. Click on **"Kali Linux"** once to select it.
4. Click the green **"Start"** button at the top.
5. A new window opens and Kali Linux boots up — wait for the login screen.
6. Type username: `kali` → press Tab or click the password field → type password: `kali` → press **Enter**.
7. The Kali Linux desktop appears.
8. To open a **Terminal** (command window) in Kali:
   - **Right-click** anywhere on the desktop.
   - Click **"Open Terminal Here"**.
   - A black terminal window opens.

---

## Step 1 — Deploy Juice Shop on Windows (Simulated IoT Device)

> This runs the vulnerable web app that acts as our fake IoT device.

1. Open **Command Prompt** on Windows (press `Win + R` → `cmd` → Enter).
2. Type this command and press **Enter**:
   ```
   docker run -d -p 8090:3000 --name juiceshop bkimminich/juice-shop
   ```
3. Wait for it to finish (it prints a long ID string — that means it worked).
4. Open your **web browser** (Chrome or Edge).
5. In the address bar, type: `http://localhost:8090` and press **Enter**.
6. The **OWASP Juice Shop** website loads — this is your simulated IoT dashboard. ✅

---

## Step 2 — Find Your Windows IP Address

1. In **Command Prompt** on Windows, type and press Enter:
   ```
   ipconfig
   ```
2. Scroll through the output.
3. Look for **"Wireless LAN adapter Wi-Fi"** section.
4. Find the line that says **"IPv4 Address"** — note down that number (looks like `192.168.x.x`).

---

## Step 3 — Perform Network Scan from Kali Linux

1. Switch to your **Kali Linux** window.
2. Open the **Terminal**.
3. Type this command (replace the IP with the Windows IP you found in Step 2):
   ```
   nmap -sV 192.168.0.148
   ```
   *(Example: if your Windows IP was `192.168.0.148`, use that number)*
4. Press **Enter** and wait — Nmap scans all ports. This takes about 1–2 minutes.
5. Results appear showing:
   - **PORT** — port numbers found open
   - **STATE** — open/closed
   - **SERVICE** — what is running (http, mysql, etc.)
   - **VERSION** — software version details

---

## Step 4 — Understand the Nmap Version Scan Output

The output will look like this (example):
```
PORT     STATE    SERVICE   VERSION
135/tcp  filtered msrpc
139/tcp  filtered netbios-ssn
445/tcp  filtered microsoft-ds
8000/tcp open     http
8090/tcp open     http      Juice Shop
```

- **Port 8090 open** means the IoT device web dashboard is accessible.
- **Service fingerprint** shows what software is running — useful for attackers.

---

## Step 5 — Access the IoT Device Web Dashboard

1. In your **web browser** on Windows, go to:
   ```
   http://localhost:8090
   ```
2. The **OWASP Juice Shop** page loads showing products like "Apple Juice", "Apple Pomace", etc.
3. This represents the IoT device's web dashboard.

---

## Step 6 — Test Default / Weak Credentials

1. On the Juice Shop page, look at the **top-right corner** — click the **person icon** or **"Account"**.
2. Click **"Login"**.
3. A login page appears with **Email** and **Password** fields.
4. In the **Email** field, type:
   ```
   admin@juice-sh.op
   ```
5. In the **Password** field, type:
   ```
   admin123
   ```
6. Click the **"Log in"** button.
7. If login succeeds → a green banner appears at the top saying you solved a challenge. ✅
8. This proves the IoT device uses **default/weak credentials** — a major security risk.

---

## Step 7 — Analyse Unencrypted HTTP Communication

1. On the Juice Shop page, press **F12** on your keyboard.
2. The **Developer Tools** panel opens (usually on the right or bottom of the browser).
3. Click the **"Network"** tab at the top of the Developer Tools panel.
4. Now click around on the Juice Shop website — browse products, click items.
5. You will see rows appearing in the Network tab — each row is a network request.
6. Click any row — on the right side you can see **Request Headers** and **Response**.
7. Notice all communication is over plain **HTTP** (not HTTPS) — data is visible in plain text.

---

## Observations Summary

| Finding | Detail |
|---------|--------|
| Open Port | Port 8090 accessible over network |
| Service Exposed | Juice Shop fingerprint visible |
| System Ports | Windows ports 135, 139, 445 visible |
| Authentication | Default credentials worked (admin) |
| Encryption | HTTP only — plain text, no SSL/TLS |

---

---

# Experiment 17: Disk Image Creation and Analysis

**Aim:** Create a forensic disk image using dc3dd and analyse it using Autopsy in Kali Linux.

---

## 🔷 Opening Kali Linux Terminal

1. Open **VirtualBox** → Start **Kali Linux** (see Experiment 16 for how).
2. Once on Kali desktop, press `Ctrl + Alt + T` — a terminal window opens.

---

## Step 1 — Open Terminal in Kali Linux

1. Press `Ctrl + Alt + T` on your keyboard.
2. A black terminal window opens with a prompt like: `┌──(kali㉿kali)-[~]`
3. The blinking cursor after `$` is where you type commands.

---

## Step 2 — Create Sample Evidence File

> We create a fake file that will be "discovered" during forensic analysis later.

1. In the terminal, type this command exactly and press **Enter**:
   ```
   echo "Cybersecurity Lab Evidence" > evidence.txt
   ```
2. Verify the file was created — type and press Enter:
   ```
   ls
   ```
3. You will see `evidence.txt` listed among other files and folders. ✅
4. To see the contents of the file, type and press Enter:
   ```
   cat evidence.txt
   ```
   Output: `Cybersecurity Lab Evidence`

---

## Step 3 — Identify the Disk Partition

1. In the terminal, type and press **Enter**:
   ```
   lsblk
   ```
2. Output appears showing storage devices like:
   ```
   NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
   sda      8:0    0  80.1G  0 disk /
   └─sda1   8:1    0  80.1G  0 part /
   sr0     11:0    1 1024M  0 rom
   ```
3. Note: **`sda`** is the main disk and **`sda1`** is the partition — we will use `/dev/sda1` for imaging.

---

## Step 4 — Install dc3dd (if needed)

1. In the terminal, type and press **Enter**:
   ```
   sudo apt install dc3dd
   ```
2. It asks for a password — type `kali` and press Enter (nothing will appear while typing).
3. It asks **"Do you want to continue? [Y/n]"** — type `Y` and press Enter.
4. Wait for installation to complete. You will see it finish with a prompt again.

---

## Step 5 — Create a Practice Disk Image File

> Instead of imaging the actual system disk (which takes hours), we create a small 100MB practice disk.

1. In the terminal, type and press **Enter**:
   ```
   dd if=/dev/zero of=practice_disk.dd bs=1M count=100
   ```
   - `if=/dev/zero` → reads from a blank source
   - `of=practice_disk.dd` → saves to a file called `practice_disk.dd`
   - `bs=1M count=100` → creates a 100MB file

2. Wait for it to finish. Output will show:
   ```
   100+0 records in
   100+0 records out
   104857600 bytes (105 MB, 100 MiB) copied
   ```

3. Now format it as a filesystem — type and press Enter:
   ```
   mkfs.ext4 practice_disk.dd
   ```
4. Output shows filesystem creation details — wait for it to finish.

---

## Step 6 — Verify the Image

1. Check the image file exists — type and press Enter:
   ```
   ls -lh /home/kali/disk_image.dd
   ```
2. If you used the dc3dd command to create a full system image, check the acquisition log:
   ```
   cat /home/kali/acquisition.log
   ```
   The log shows: image size, MD5 hash value, and acquisition details.

---

## Step 7 — Open Autopsy (Forensic Analysis Tool)

1. In the terminal, type and press **Enter**:
   ```
   autopsy
   ```
   *(If it requires sudo: type `sudo autopsy` instead)*

2. The terminal shows output like:
   ```
   Autopsy Forensic Browser
   http://www.sleuthkit.org/autopsy/
   ver 2.24
   ...
   Local Port: 9999
   Open an HTML browser and paste this URL:
       http://localhost:9999/autopsy
   ```
3. **Do NOT close this terminal** — keep it open.

---

## Step 8 — Open Autopsy in the Browser

1. Open the **Firefox** browser in Kali Linux:
   - Click the **Firefox icon** in the taskbar at the bottom, OR
   - Right-click desktop → click **"Open Terminal"** → type `firefox &` → press Enter.
2. In the Firefox address bar, type:
   ```
   http://localhost:9999/autopsy
   ```
3. Press **Enter**.
4. The **Autopsy Forensic Browser** web interface loads. You see buttons: **Open Case**, **New Case**, **Help**.

---

## Step 9 — Create a New Case in Autopsy

1. On the Autopsy home page, click **"New Case"** button.
2. A form appears:
   - **Case Name:** type `CyberForensicsLab`
   - **Description:** type `Creating a new forensic record`
   - **Investigator Names:** type your name in field `a`
3. Click **"New Case"** button at the bottom.
4. Next screen says the case directory was created and asks to add a host.
5. From the dropdown next to "Please select your name from the list", select your name.
6. Click **"Add Host"** button.

---

## Step 10 — Add Host Details

1. A form appears — **"Add a New Host"**:
   - **Host Name:** type `KaliLabMachine`
   - **Description:** type `forensic disk image investigation`
   - Leave **Time Zone**, **Time Skew**, **Alert Hash**, **Ignore Hash** all blank.
2. Click **"Add Host"** button.
3. Next screen says host was created and shows an **"Add Image"** button.
4. Click **"Add Image"** button.

---

## Step 11 — Add the Disk Image to Autopsy

1. A form appears — **"Add a New Image"**:
   - **Location:** type the full path to the disk image:
     ```
     /home/kali/disk_image.dd
     ```
   - **Type:** click the radio button for **"Partition"**.
   - **Import Method:** click **"Symlink"**.
2. Click **"Next"** button.
3. **Image File Details** screen appears:
   - Under **Data Integrity**: select **"Calculate the hash value for this image"**.
   - Check the box **"Verify hash after importing"**.
4. Click **"Add"** button.
5. Autopsy calculates the MD5 hash and adds the image — wait for it to finish.
6. Click **"OK"** when done.

---

## Step 12 — Analyse Evidence in Autopsy

1. You are now in the main analysis screen. You see tabs at the top:
   **File Analysis | Keyword Search | File Type | Image Details | Meta Data | Data Unit**

2. **To browse files:**
   - Click **"File Analysis"** tab.
   - You will see directories listed. Click on folders to expand them.
   - Click any file to see its contents in the panel below.

3. **To search for keywords:**
   - Click **"Keyword Search"** tab.
   - In the text box, type a word to search (e.g., `evidence`).
   - Click **"Search"** button.
   - Results appear showing which files contain that word.

4. **To view deleted files:**
   - In File Analysis, look for files marked in **red** — those are deleted files.
   - Click on them to recover/view their content.

5. **To sort by file type:**
   - Click **"File Type"** tab.
   - Click **"Sort Files by Type"**.
   - Then click **"View Sorted Files"** to see results.

---

---

# Experiment 18: Log File Analysis for Incident Detection

**Aim:** Analyse system log files in Kali Linux to detect suspicious activities like failed login attempts and identify suspicious IP addresses.

---

## 🔷 Opening Terminal in Kali Linux

- Press `Ctrl + Alt + T` — black terminal window opens.

---

## Step 1 — Navigate to the Log Directory

1. In the terminal, type and press **Enter**:
   ```
   cd /var/log
   ```
2. Now type and press **Enter**:
   ```
   ls
   ```
3. You will see a list of log files and folders:
   - `apache2` → web server logs
   - `journal` → system logs (important!)
   - `wtmp` → successful login records
   - `btmp` → failed login records
   - `dpkg.log` → software installation logs
   - `nginx` → web server logs

---

## Step 2 — View Successful Logins

1. In the terminal, type and press **Enter**:
   ```
   last
   ```
2. A list appears showing:
   - **Who** logged in
   - **From where** (terminal or IP)
   - **When** they logged in and out
3. Look for any unusual logins at odd times or from unknown locations.

---

## Step 3 — View All System Logs

1. Type and press **Enter**:
   ```
   journalctl | less
   ```
2. Logs scroll on screen. Use these keys to navigate:
   - Press `Space` bar to go to the **next page**.
   - Press `b` to go **back** one page.
   - Press `q` to **quit** and return to normal terminal.

---

## Step 4 — Search for Failed SSH Attempts

1. Type and press **Enter**:
   ```
   journalctl | grep "Failed"
   ```
2. Only lines containing the word **"Failed"** are shown.
3. These lines represent failed login or connection attempts — look suspicious if many appear.

---

## Step 5 — Search for SSH Activity

1. Type and press **Enter**:
   ```
   journalctl | grep ssh
   ```
2. All SSH-related log entries appear.
3. You can see when SSH started, when connections were made, and any failures.

---

## Step 6 — Search for System Errors

1. Type and press **Enter**:
   ```
   journalctl | grep -i error
   ```
2. All lines containing the word "error" (in any case — upper or lower) appear.
3. Many errors repeated from the same source could indicate a problem or attack.

---

## Step 7 — Analyse Apache Web Server Logs

1. Navigate to Apache log folder — type and press Enter:
   ```
   cd /var/log/apache2
   ```
2. List files inside — type and press Enter:
   ```
   ls
   ```
   You will see: `access.log`, `error.log`, `other_vhosts_access.log`

3. View the access log — type and press Enter:
   ```
   sudo less access.log
   ```
   *(Type `kali` when asked for password)*
   - Each line shows: IP address, date/time, request made, and response code.
   - Press `q` to quit when done.

4. Find all 404 errors (page not found — may indicate scanning) — type and press Enter:
   ```
   grep "404" /var/log/apache2/access.log
   ```

---

## Step 8 — View Package/Software Installation Logs

1. Type and press **Enter**:
   ```
   less /var/log/dpkg.log
   ```
2. Shows all software that was installed, removed, or updated.
3. Look for unexpected software installations.
4. Press `q` to quit.

---

## Step 9 — Real-Time Log Monitoring

1. Type and press **Enter**:
   ```
   sudo journalctl -f
   ```
2. Logs appear in **real time** — every new log entry prints automatically.
3. Open another window or make something happen on the system — you will see new log entries appear instantly.
4. Press `Ctrl + C` to stop and return to normal terminal.

---

## Step 10 — Simulate a Failed SSH Login (To Generate Test Logs)

### First, ensure SSH is running:

1. Type and press **Enter**:
   ```
   sudo service ssh start
   ```
2. Find your IP address — type and press Enter:
   ```
   ip a
   ```
3. Look for the line starting with `inet` under `eth0` — note the IP (e.g., `10.0.2.15`).

### Generate Failed Login Attempts:

1. Type and press **Enter**:
   ```
   ssh fakeuser@localhost
   ```
2. A prompt asks **"Are you sure you want to continue connecting (yes/no)?"** — type `yes` and press Enter.
3. It asks for a password — type anything wrong (e.g., `wrongpassword`) and press Enter.
4. It says **"Permission denied"** — this is a failed login.
5. Repeat steps 1–4 a total of **5 to 10 times** to generate multiple failed entries.
6. Press `Ctrl + C` when done.

---

## Step 11 — Analyse the Failed Login Logs

1. Check for failed password entries — type and press Enter:
   ```
   journalctl | grep "Failed password"
   ```
   You should see lines like:
   ```
   Failed password for kali from 127.0.0.1 port ...
   ```

2. Extract just the IP addresses from those entries — type and press Enter:
   ```
   journalctl | grep "Failed password" | awk '{print $11}'
   ```
   Output: `127.0.0.1` (repeated for each failure)

3. Count how many times each IP failed — type and press Enter:
   ```
   journalctl | grep "Failed password" | awk '{print $11}' | sort | uniq -c | sort -nr
   ```
   Output example:
   ```
   10  127.0.0.1
   ```
   This means IP `127.0.0.1` failed login **10 times** — this looks like a **brute force attack**.

4. View recent activity only (last 10 minutes) — type and press Enter:
   ```
   journalctl --since "10 minutes ago"
   ```

---

## 📊 Quick Reference — Suspicious Signs

| Check | Command | Suspicious If |
|-------|---------|--------------|
| Failed logins | `lastb` | Many attempts from same IP |
| SSH activity | `journalctl \| grep ssh` | Unknown connections |
| Login history | `last` | Logins at odd hours |
| System errors | `journalctl \| grep -i error` | Repeated identical errors |
| Web requests | `grep "404" access.log` | Many failed requests |

---

---

# Experiment 20: Privacy Audit of WhatsApp & Facebook

**Aim:** Analyse privacy, network behaviour, and security risks of WhatsApp and Facebook using Wireshark and Burp Suite.

---

## 🔷 How to Open Kali Linux and Terminal

1. Open **VirtualBox** → select Kali Linux → click **Start**.
2. Log in: username `kali`, password `kali`.
3. Press `Ctrl + Alt + T` to open Terminal.

---

## PART A — Setting Up WhatsApp Desktop App Using Nativefier

---

## Step 1 — Install Node.js and npm

1. In the Kali Terminal, type and press **Enter**:
   ```
   sudo apt update
   ```
   *(Type password `kali` if asked, then Enter)*
2. Wait for update to finish, then type and press **Enter**:
   ```
   sudo apt install nodejs npm -y
   ```
3. Wait for installation to complete.

---

## Step 2 — Install Nativefier

1. Type and press **Enter**:
   ```
   sudo npm install -g nativefier
   ```
2. Wait for it to finish — it shows a progress bar and then says `added X packages`.

---

## Step 3 — Create WhatsApp Desktop App

1. Type and press **Enter**:
   ```
   nativefier https://web.whatsapp.com
   ```
2. It processes for a few minutes — you will see messages like:
   - `Processing options...`
   - `Preparing Electron app...`
   - `Converting icons...`
   - `Packaging...`
   - `Finalizing build...`
3. When done, it says `App built to /home/kali/WhatsAppWeb-linux-x64`.

---

## Step 4 — Run the WhatsApp App

1. Navigate into the created folder — type and press **Enter**:
   ```
   cd WhatsAppWeb-linux-x64
   ```
2. List files to confirm — type and press Enter:
   ```
   ls
   ```
   You should see a file called `WhatsAppWeb`.
3. Run it — type and press **Enter**:
   ```
   ./WhatsAppWeb
   ```
4. A WhatsApp window opens showing a **QR code scan** screen.

---

## Step 5 — Log Into WhatsApp

1. On your **mobile phone**, open the **WhatsApp** app.
2. Tap the **three dots menu** (top-right in WhatsApp) → tap **"Linked Devices"**.
3. Tap **"Link a Device"**.
4. Point your phone camera at the **QR code** on the Kali Linux screen.
5. WhatsApp loads on the Kali Linux window showing your chats. ✅

---

## PART B — Analyse WhatsApp Using Exodus Privacy

---

## Step 6 — Check WhatsApp Trackers Online

1. Open **Firefox** browser in Kali Linux:
   - Click the **Firefox icon** in the taskbar.
2. In the address bar, type and press Enter:
   ```
   https://reports.exodus-privacy.eu.org
   ```
3. The Exodus Privacy website loads.
4. In the **"Search a report"** box, type `WhatsApp` and press Enter.
5. Click on the WhatsApp result.
6. You will see:
   - Number of **trackers** found in the app.
   - Number of **permissions** the app requests.
   - Details of each tracker (e.g., Google Analytics, Crashlytics, Facebook Analytics).
7. Note down these numbers for your lab record.

---

## PART C — Capture WhatsApp Traffic Using Wireshark

---

## Step 7 — Open Wireshark

1. In Kali Linux, click the **Start/Applications menu** (top-left of desktop).
2. Click **"01 - Reconnaissance"** → click **"Wireshark"**.
   OR in the terminal type `wireshark` and press Enter.
3. Wireshark opens. You see a list of **network interfaces** (eth0, lo, docker0, etc.).
4. Click on **"eth0"** (or whichever shows traffic activity — you can see tiny bar charts moving next to active interfaces).
5. Click the **blue shark fin button** at the top-left to **start capturing**.

---

## Step 8 — Generate WhatsApp Traffic

1. Switch to your **WhatsApp** window on Kali.
2. Click on any chat.
3. Type a message and press **Enter** to send.
4. Send an image — click the **paperclip/attachment icon** in the chat box.
5. Open another chat, scroll through messages.

---

## Step 9 — Apply Filters in Wireshark

1. Switch back to **Wireshark**.
2. At the top of Wireshark, there is a **filter bar** (a long white input box).
3. Click in the filter bar and type:
   ```
   tls
   ```
4. Press **Enter**.
5. Only TLS (encrypted) packets are shown.
6. You will see packets going to/from WhatsApp servers but the **content is encrypted** — you cannot read the messages.

7. Now change the filter to:
   ```
   dns
   ```
8. Press **Enter**. You can see DNS queries — domains that WhatsApp contacts:
   - `whatsapp.net`
   - `facebook.com`

> **Key Observation:** Message content is NOT visible because WhatsApp uses End-to-End Encryption. But metadata (IP addresses, domains, timing) is visible.

---

## PART D — Audit Facebook Using Burp Suite

---

## Step 10 — Open Burp Suite in Kali Linux

1. In Kali Linux, click the **Applications menu** → search for **Burp Suite**.
   OR in the terminal type `burpsuite` and press Enter.
2. Burp Suite opens.
3. Click **"Temporary project"** → click **"Next"**.
4. Click **"Use Burp defaults"** → click **"Start Burp"**.

---

## Step 11 — Configure Burp Suite Proxy

1. In Burp Suite, click the **"Proxy"** tab.
2. Click **"Proxy settings"**.
3. Under **Proxy Listeners**, the listener shows `127.0.0.1:8080` — this is fine for local use.

---

## Step 12 — Configure Firefox to Use Burp Suite as Proxy

1. Open **Firefox** in Kali Linux.
2. Click the **three horizontal lines** (hamburger menu ≡) at the **top-right** of Firefox.
3. Click **"Settings"**.
4. Scroll down to the very bottom — click **"Settings"** button next to **"Network Settings"**.
5. A Connection Settings dialog opens:
   - Select the radio button **"Manual proxy configuration"**.
   - In **HTTP Proxy** field: type `127.0.0.1`
   - In **Port** field next to it: type `8080`
   - Check the box **"Also use this proxy for HTTPS"**.
6. Click **"OK"**.

---

## Step 13 — Turn On Intercept and Open Facebook

1. In Burp Suite, click **"Proxy"** tab → click **"Intercept"** sub-tab.
2. Click the button to make it say **"Intercept is on"**.
3. In **Firefox**, go to the address bar and type:
   ```
   https://www.facebook.com
   ```
4. Press **Enter**.
5. Firefox seems to pause/hang — that is because Burp Suite is intercepting the request.
6. In Burp Suite, you will see the captured request in the Intercept tab.
7. Click the **"Forward"** button in Burp to let the request through.
8. Keep clicking **Forward** until Facebook loads in Firefox.
9. Log into Facebook using a test account.

---

## Step 14 — Observe Tracking in Burp Suite HTTP History

1. Turn off intercept for now — click the button to make it say **"Intercept is off"**.
2. Browse around Facebook — click on posts, scroll, click ads.
3. In Burp Suite, click **"Proxy"** tab → click **"HTTP history"** sub-tab.
4. You will see dozens of requests — look for:
   - Requests containing **"datr"** or **"fr"** in the URL — these are Facebook tracking cookies.
   - Requests to third-party domains (analytics, ad networks).
   - **Session IDs** in the request headers.

---

## Step 15 — Use Browser Developer Tools to See Tracking

1. In **Firefox** on the Facebook page, press **F12** on your keyboard.
2. Developer Tools panel opens.
3. Click the **"Network"** tab.
4. Reload the page — press **F5**.
5. You will see ALL network requests the Facebook page makes.
6. Look for requests going to:
   - `connect.facebook.net`
   - `pixel.facebook.com`
   - Analytics and ad-tracking scripts.
7. This shows Facebook's **extensive tracking behaviour**.

---

## PART E — Data Breach Case Study

### Case Study 1: Facebook Data Breach (2021)
- **533 million** users affected worldwide.
- Data leaked: Phone numbers, Email addresses, Names, Birthdates.
- Cause: An **API vulnerability** allowed mass scraping of user data.

### Case Study 2: WhatsApp Pegasus Attack
- **Spyware** was installed on phones via a missed WhatsApp call.
- The spyware gave attackers **full remote access** to the device.
- Users did not even need to answer the call.

### Step 16 — Check if Your Email Was in a Data Breach

1. In **Firefox**, go to:
   ```
   https://haveibeenpwned.com
   ```
2. In the search box, type an email address.
3. Click **"pwned?"** button.
4. Results show which data breaches (if any) included that email.

---

## Result

Privacy audit was successfully performed. WhatsApp ensures secure communication through end-to-end encryption (message content not visible). Facebook shows extensive tracking via cookies and third-party scripts. Data breach case studies highlight risks of API vulnerabilities and zero-click exploits.

---

---

# Experiment 21: Security Audit and Risk Assessment on Windows

**Aim:** Perform a security audit on a Windows system, identify potential vulnerabilities, and suggest mitigation strategies.

> ✅ This entire experiment is done on **Windows** — no Kali Linux needed.

---

## Step 1 — Check System Information

1. Press `Windows key + R` on your keyboard simultaneously.
2. A small **"Run"** dialog box appears at the bottom-left corner of the screen.
3. Type `msinfo32` and press **Enter**.
4. The **System Information** window opens.
5. Note down the following from the right panel:
   - **OS Name** (e.g., Microsoft Windows 11 Home)
   - **System Type** (e.g., x64-based PC)
   - **Processor** (e.g., Intel Core i5)
   - **BIOS Mode** (should say UEFI for modern secure systems)
   - **Secure Boot State** (should say **On** for good security)
6. Close the window when done.

> **What to look for:** Secure Boot = On means the system is protected from unauthorized boot software.

---

## Step 2 — Check Windows Updates

1. Click the **Start button** (Windows logo at the bottom-left).
2. Click the **gear icon ⚙️** (Settings).
3. In the Settings window, click **"Windows Update"** (on the left panel, scroll down to find it).
4. Click the **"Check for updates"** button.
5. Windows checks for available updates.
6. Note whether updates are **available** or the system is **up to date**.

> **What to look for:** Any updates listed as "Pending restart" or "Available" means the system has unpatched vulnerabilities.

---

## Step 3 — Check Firewall Status

1. Click the **Start button**.
2. In the search bar, type `Windows Defender Firewall` and press **Enter**.
3. The Windows Defender Firewall window opens.
4. You will see two sections:
   - **Private networks** — shows a green shield ✅ (active) or red X (inactive).
   - **Guest or public networks** — same.
5. Check whether the firewall is **On** for both.
6. Also look for any message like "These settings are being managed by [Kaspersky/other antivirus]" — this means a third-party firewall is active.

> **What to look for:** Firewall should be active on all network types. If managed by Kaspersky, that is still fine.

---

## Step 4 — Check Antivirus Status

1. Click the **Start button**.
2. Type `Windows Security` and press **Enter**.
3. The Windows Security window opens with a dashboard.
4. Click **"Virus & threat protection"** (shield icon on the left panel).
5. Look at:
   - **Current threats** — should say "No current threats" ✅
   - **Protection settings** — should say "No actions needed" ✅
   - **Protection updates** — should say "No actions needed" ✅
   - The antivirus name shown (e.g., Kaspersky) — confirms it is active.

> **What to look for:** If real-time protection is OFF, the system is vulnerable to malware.

---

## Step 5 — Check Password and Account Security

1. Click the **Start button** → click the **gear icon** (Settings).
2. In Settings, click **"Accounts"** on the left panel.
3. Click **"Sign-in options"**.
4. You will see a list of available sign-in methods:
   - **Facial recognition (Windows Hello)** — advanced biometric security
   - **Fingerprint recognition** — biometric
   - **PIN (Windows Hello)** — should say "Added" if configured ✅
   - **Password** — should be set ✅
5. Note which methods are configured.

> **What to look for:** At minimum, a PIN or Password should be set. If no sign-in method is configured, anyone can access the system.

---

## Step 6 — Check Installed Applications

1. Click the **Start button** → click the **gear icon** (Settings).
2. Click **"Apps"** on the left panel.
3. Click **"Installed apps"**.
4. A list of all installed software appears.
5. Scroll through the list carefully.
6. Look for:
   - Any software you don't recognise.
   - Cracked or pirated tools (these often contain malware).
   - Outdated software with old dates.

> **What to look for:** Unknown or suspicious software should be investigated and removed.

---

## Step 7 — Check Startup Programs

1. Press `Ctrl + Shift + Esc` all at once.
2. **Task Manager** opens.
3. Click on the **"Startup apps"** tab (in the left panel or top tab row).
4. A list of programs that start automatically when Windows boots.
5. For each item, look at:
   - **Status** (Enabled/Disabled)
   - **Startup impact** (High/Medium/Low/None)
   - **Publisher** — unknown publishers are suspicious.
6. If you see an unknown app with "Enabled" status, right-click it → click **"Disable"**.

> **What to look for:** Unnecessary startup programs slow down the system and increase the attack surface.

---

## Step 8 — Check Network Security

1. Click the **Start button** → click the **gear icon** (Settings).
2. Click **"Network & Internet"** on the left panel.
3. You will see your current connection at the top (e.g., Wi-Fi — Connected, secured).
4. Click on your **Wi-Fi network name**.
5. Look at **"Network profile type"**:
   - **Public network** — more secure, device is less visible on network ✅
   - **Private network** — more accessible to other devices (use only on trusted networks).
6. Note the network name and profile type.

> **What to look for:** On public/college networks, always use "Public" network profile.

---

## Step 9 — Check Browser Security

1. Open **Google Chrome** (click it in the taskbar or Start menu).
2. Click the **three dots menu** (⋮) at the **top-right corner** of Chrome.
3. Click **"Settings"**.
4. In the left panel, click **"Privacy and security"**.
5. Click **"Security"**.
6. Under **"Safe Browsing"**, check which option is selected:
   - **Enhanced protection** — best ✅✅
   - **Standard protection** — good ✅
   - **No protection** — dangerous ❌
7. Make sure "Standard protection" or "Enhanced protection" is selected.

> **What to look for:** Safe Browsing should be ON to protect against phishing and malware sites.

---

## Step 10 — Check Data Backup Status

1. Click the **Start button** → click the **gear icon** (Settings).
2. In the search box inside Settings, type `Backup` and click **"Windows backup settings"**.
3. You will see the backup screen showing:
   - **OneDrive** sync status.
   - **App list** backup status.
   - **Preferences** backup status.
4. If OneDrive shows "Not syncing" — backup is not configured.
5. If all show green checkmarks — backup is properly set up. ✅

> **What to look for:** No backup = high risk of data loss from ransomware or hardware failure.

---

## Step 11 — Risk Assessment Summary Table

Fill this in based on your observations:

| Asset | Threat | Vulnerability Found | Risk Level | Recommended Solution |
|-------|--------|--------------------|-----------|--------------------|
| Personal Files | Data Loss / Ransomware | OneDrive backup not configured | **High** | Enable OneDrive sync |
| Operating System | Malware / Exploits | Pending Windows updates | **High** | Install all updates immediately |
| Network | Eavesdropping / Hacking | Connected to public network | **Medium** | Use Private profile on trusted networks |
| User Account | Unauthorized Access | Weak/no PIN | **High** | Set a strong PIN and password |
| Applications | Malware infection | Unknown startup apps | **Medium** | Disable unknown startup programs |
| Browser | Phishing / Malware | Safe Browsing off | **High** | Enable Standard/Enhanced protection |

---

## Step 12 — Security Recommendations

### ✅ Technical Controls
- Enable Windows Defender Firewall on all network types.
- Keep antivirus (Kaspersky/Defender) active and up to date.
- Install all pending Windows Updates regularly.
- Enable OneDrive or external drive backup.

### ✅ Administrative Controls
- Use strong passwords (min 12 characters — mix of letters, numbers, symbols).
- Enable Windows Hello PIN or fingerprint.
- Disable unnecessary startup applications.

### ✅ User Awareness
- Never click suspicious email links.
- Avoid downloading cracked/pirated software.
- Always use Safe Browsing in Chrome/Edge.
- Lock your PC when stepping away (`Windows key + L`).

---

## Result

The Windows security audit successfully identified potential vulnerabilities including missing backups, pending updates, and startup program risks. Mitigation strategies including enabling backup, updating software, and strengthening authentication were identified.

---

---

## 📝 General Tips for All Experiments

| Situation | What to do |
|-----------|-----------|
| Terminal command not found | Make sure you typed it exactly — Linux is case-sensitive |
| Emulator very slow | Give it 3–5 minutes to fully load; don't click repeatedly |
| Burp Suite not intercepting | Check "All interfaces" is selected AND intercept is ON |
| `sudo` command asks password | Type `kali` and press Enter (nothing shows while typing — that's normal) |
| Kali Linux frozen | Right-click desktop → Open Terminal → try the command again |
| ADB device not found | Make sure the emulator is fully booted before running ADB commands |
| Browser shows proxy error | Confirm Burp Suite is open and running before browsing |

exp3
1.dd if=/dev/zero of=/home/bhavagna/practice_disk.dd bs=1M count=100
2.mkfs.ext4 /home/bhavagna/practice_disk.dd
3.mkdir /home/bhavagna/mnt
4.sudo mount -o loop /home/bhavagna/practice_disk.dd /home/bhavagna/mnt
5.echo "check" | sudo tee /home/bhavagna/mnt/evidence.txt
6.sudo umount /home/bhavagna/mnt
7.sudo autopsy
