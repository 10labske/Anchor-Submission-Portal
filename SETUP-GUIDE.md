# Complete Setup Guide — Property Submission App

This guide walks you through **everything** from zero to a working site. Follow the steps **in order**. Do not skip ahead.

**Time needed:** about 20–30 minutes  
**What you need:** A Google account (Gmail)

---

## Overview — What You Are Building

```
User fills form on website
        ↓
Website sends data to Google Apps Script (Web App URL)
        ↓
Apps Script saves files to Google Drive + row to Google Sheet
```

You will create **3 things in Google** and connect them to **3 files on your computer**.

| # | What | Where it lives |
|---|------|----------------|
| 1 | Google Sheet | Stores form submissions (spreadsheet) |
| 2 | Google Drive folder | Stores property photos & videos |
| 3 | Google Apps Script | Backend that connects website → Sheet + Drive |
| 4 | Website files | `index.html`, `styles.css`, `script.js` on your server |

---

## What Goes Where (Important)

| Item | Public (website / GitHub)? | Where to put it |
|------|---------------------------|-----------------|
| Web App URL | **Yes** — safe to expose | `script.js` |
| Spreadsheet ID | **No** — keep private | `appsscript.gs` only (in Google) |
| Drive Folder ID | **No** — keep private | `appsscript.gs` only (in Google) |
| `index.html`, `styles.css`, `script.js` | Yes | Your server / GitHub |
| `appsscript.gs`, `appsscript.json` | **No** — do not upload to public GitHub | Google Apps Script editor only |

---

# PART 1 — Google Setup

Use the **same Google account** for all steps below.

---

## Step 1 — Create the Google Sheet

**Purpose:** Every property submission becomes one row in this spreadsheet.

1. Go to [Google Sheets](https://sheets.google.com)
2. Click **Blank** to create a new spreadsheet
3. Rename it (top left): **Property Submissions**
4. Leave the sheet empty — headers are created automatically by the script
5. Copy the **Spreadsheet ID** from the browser address bar:

```
https://docs.google.com/spreadsheets/d/COPY_THIS_PART/edit
                                      ↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑
                                      Spreadsheet ID
```

**Example:**
```
https://docs.google.com/spreadsheets/d/1QUOUX7PWHgP4oXRoZvmXsbpxAKJr4Itunv8tlu9woCA/edit
```
Spreadsheet ID = `1QUOUX7PWHgP4oXRoZvmXsbpxAKJr4Itunv8tlu9woCA`

📝 **Save this ID** — you will paste it in Step 3.

✅ **Done when:** You have a blank spreadsheet and its ID copied.

---

## Step 2 — Create the Google Drive Folder

**Purpose:** All property photos and videos are stored inside this folder.

1. Go to [Google Drive](https://drive.google.com)
2. Click **New** → **Folder**
3. Name it exactly: **Property Uploads**
4. **Double-click** to open the folder (you must be inside it)
5. Copy the **Folder ID** from the browser address bar:

```
https://drive.google.com/drive/folders/COPY_THIS_PART
                                            ↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑
                                            Folder ID
```

**Example:**
```
https://drive.google.com/drive/folders/1nXYcAVd-oSHSQiM0bRVizsOI9aPCmbJg
```
Folder ID = `1nXYcAVd-oSHSQiM0bRVizsOI9aPCmbJg`

📝 **Save this ID** — you will paste it in Step 3.

> **Do NOT** create `images/` or `videos/` subfolders manually. The script creates them automatically for each property.

✅ **Done when:** You have a folder named "Property Uploads" and its ID copied.

---

## Step 3 — Create Google Apps Script Project

**Purpose:** This is the backend brain — it receives form data and saves to Drive + Sheet.

### 3a. Create the project

1. Go to [Google Apps Script](https://script.google.com)
2. Click **New project**
3. Rename the project (top left): **Property Submission API**

### 3b. Paste the backend code

1. In the left sidebar, click **Code.gs**
2. **Select all** default code (Ctrl+A) and **delete** it
3. On your computer, open the file `appsscript.gs` from this project folder
4. **Copy everything** and paste into `Code.gs`

### 3c. Add your Sheet and Folder IDs

At the **top** of `Code.gs`, find these two lines and replace with your IDs from Steps 1 & 2:

```javascript
const PARENT_FOLDER_ID = 'YOUR_DRIVE_FOLDER_ID';   // Step 2
const SPREADSHEET_ID = 'YOUR_SPREADSHEET_ID';       // Step 1
```

**Example:**
```javascript
const PARENT_FOLDER_ID = '1nXYcAVd-oSHSQiM0bRVizsOI9aPCmbJg';
const SPREADSHEET_ID = '1QUOUX7PWHgP4oXRoZvmXsbpxAKJr4Itunv8tlu9woCA';
```

Press **Ctrl+S** to save.

✅ **Done when:** `Code.gs` has your full code with real IDs at the top.

---

## Step 4 — Set Up appsscript.json (Settings File)

**Purpose:** Tells Google the timezone and that "Anyone" can call your API.

> ⚠️ This is a **second file** in Apps Script — not the same as `Code.gs`.

### 4a. Show the file

1. Click the **gear icon** (⚙️) on the left → **Project settings**
2. Scroll to **General settings**
3. Turn **ON**: **"Show appsscript.json manifest file in editor"**
4. Click **Editor** (`<>` icon) to go back

You should now see **two files** in the left sidebar:

```
Files
 ├── Code.gs           ← backend code (Step 3)
 └── appsscript.json   ← settings (this step)
```

### 4b. Paste the settings

1. Click **appsscript.json** in the sidebar
2. Select all (Ctrl+A) and delete
3. On your computer, open `appsscript.json` from this project folder
4. Copy everything and paste into Apps Script
5. Press **Ctrl+S** to save

It should look like this:

```json
{
  "timeZone": "America/New_York",
  "dependencies": {},
  "exceptionLogging": "STACKDRIVER",
  "runtimeVersion": "V8",
  "webapp": {
    "access": "ANYONE_ANONYMOUS",
    "executeAs": "USER_DEPLOYING"
  }
}
```

✅ **Done when:** You see both `Code.gs` and `appsscript.json` in the sidebar.

---

## Step 5 — Run setupProject (Test Connection)

**Purpose:** Verifies the script can access your Drive folder and Sheet.

### 5a. Find the Run button

1. Make sure you are on **Code.gs** (not appsscript.json)
2. Press **Ctrl+S** to save
3. Look at the **top toolbar**:

```
[ Undo ] [ Redo ]   ▶ Run   🐛 Debug   [ setupProject ▼ ]   Execution log
                                              ↑
                                    Click this dropdown
```

4. Click the dropdown → select **`setupProject`**
5. Click the **▶ Run** button

> **Can't see the dropdown?**
> - You must be on **Code.gs**, not `appsscript.json`
> - Save the file first (Ctrl+S)
> - Or use menu: **Run → Run function → setupProject**

### 5b. Authorize the app (first time only)

Google will show:

> **"Google hasn't verified this app"**

**This is normal** for your own personal script. You are the developer.

1. Click **Advanced**
2. Click **Go to Property Submission API (unsafe)**
3. Click **Allow**

### 5c. Check the result

1. Click **Execution log** (top right) or **View → Logs**
2. You should see:

```
Parent folder found: Property Uploads
Sheet ready: Submissions with 1 rows
Setup complete!
```

3. Open your Google Sheet — you should see a **Submissions** tab with column headers

✅ **Done when:** Logs say "Setup complete!" and Sheet has headers.

---

## Step 6 — Deploy as Web App

**Purpose:** Creates a public URL your website can send form data to.

> Deploy from the **browser** at [script.google.com](https://script.google.com) — NOT from files on your computer.

1. In Apps Script, click **Deploy** (top right)
2. Click **New deployment**
3. Click the **gear icon** ⚙️ next to "Select type"
4. Choose **Web app**
5. Fill in:

| Field | Value |
|-------|-------|
| Description | Property Submission API v1 |
| Execute as | **Me** (your email) |
| Who has access | **Anyone** |

6. Click **Deploy**
7. If asked to authorize again → repeat Step 5b (Advanced → Allow)
8. **Copy the Web App URL** — it ends in `/exec`:

```
https://script.google.com/macros/s/AKfycb.../exec
```

### Test the URL

Paste the URL in your browser. You should see:

```json
{"success":true,"message":"Property submission API is running.","version":"1.0.0"}
```

📝 **Save this URL** — you need it for Step 7.

✅ **Done when:** Browser shows the JSON success message above.

---

# PART 2 — Connect the Website

---

## Step 7 — Add Web App URL to script.js

**Purpose:** Tells your website where to send form submissions.

1. On your computer, open `script.js`
2. Find the `CONFIG` section at the top (around line 9)
3. Replace the placeholder with your Web App URL:

```javascript
const CONFIG = {
  WEB_APP_URL: 'https://script.google.com/macros/s/YOUR_DEPLOYMENT_ID/exec',
  // ...
};
```

4. Save the file

✅ **Done when:** `WEB_APP_URL` has your real `/exec` URL (not the placeholder).

---

## Step 8 — Run the Website Locally (XAMPP)

1. Make sure these files are in your XAMPP folder:
   ```
   C:\xampp\htdocs\New folder\
   ├── index.html
   ├── styles.css
   └── script.js
   ```
2. Open **XAMPP Control Panel**
3. Start **Apache**
4. Open in browser:
   ```
   http://localhost/New%20folder/
   ```

✅ **Done when:** You see the landing page with "Sell Your Property Faster".

---

## Step 9 — Test a Full Submission

1. Scroll to **Submit Your Property**
2. Fill required fields:
   - Full Name
   - Phone Number
   - Property Title
   - Property Type
   - County, City, Address
3. Upload **1–2 test images** (JPG or PNG)
4. Click **Submit Property**
5. Watch the progress bar:
   - Compressing Images...
   - Uploading Files...
   - Saving Property...
   - Submission Complete

### Verify it worked

| Check | Where | What you should see |
|-------|-------|---------------------|
| 1 | Google Sheet | New row with your form data |
| 2 | Google Drive → Property Uploads | New folder like `PropertyName_20260703_110000` |
| 3 | Inside that folder | `images/` folder with your photos |

✅ **Done when:** Sheet has a row and Drive has the images.

---

# PART 3 — Upload to GitHub

---

## Step 10 — What to Upload (and What NOT to)

### ✅ Safe to upload (public repo)

```
index.html
styles.css
script.js
README.md
SETUP-GUIDE.md
.gitignore
```

### ❌ Do NOT upload (contains private IDs)

```
appsscript.gs    ← has Spreadsheet ID and Drive Folder ID
appsscript.json  ← backend settings
```

The `.gitignore` file in this project already blocks `appsscript.gs` and `appsscript.json` from being committed.

### About the Web App URL in script.js

The Web App URL in `script.js` **is safe** to be public — it is designed to be called from the browser. Your Sheet and Drive IDs stay hidden inside Google Apps Script only.

---

## Step 11 — Push to GitHub

```bash
cd "C:\xampp\htdocs\New folder"
git init
git add index.html styles.css script.js README.md SETUP-GUIDE.md .gitignore
git commit -m "Add property submission landing page"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/YOUR_REPO.git
git push -u origin main
```

---

# PART 4 — Going Live (Production Hosting)

For the site to work online (not just localhost), host these 3 files on any static host:

- [Netlify](https://netlify.com) — drag & drop folder
- [GitHub Pages](https://pages.github.com) — free from your repo
- [Vercel](https://vercel.com)
- Your own web hosting (cPanel, etc.)

Upload only: `index.html`, `styles.css`, `script.js`

No server-side code needed — the Google Apps Script backend handles everything.

---

# Troubleshooting

## "Please configure the Web App URL"
→ You did not replace the placeholder in `script.js`. Paste your `/exec` URL.

## "Google hasn't verified this app"
→ Normal for personal scripts. Click **Advanced** → **Go to ... (unsafe)** → **Allow**.

## Can't see function dropdown in Apps Script
→ You are on `appsscript.json` instead of `Code.gs`. Switch to `Code.gs` and save.

## "Invalid response from server"
→ Redeploy with **Who has access: Anyone**. Test the URL in browser for JSON response.

## Form submits but no row in Sheet
→ Open Apps Script → **Executions** tab → check for errors. Re-run `setupProject`.

## Images not in Drive
→ Check `PARENT_FOLDER_ID` in `Code.gs` is correct. Re-authorize the script.

## "Payload too large"
→ Video file too big. Try a smaller video or fewer images per submission.

## Video compression not working locally
→ Normal on XAMPP/localhost. FFmpeg needs special server headers. Videos still upload in original format — user gets a confirmation dialog.

## After changing appsscript.gs code
→ You must create a **New deployment** (or edit deployment → New version) for changes to take effect.

---

# Quick Reference Card

| Step | Action | Result |
|------|--------|--------|
| 1 | Create Google Sheet | Spreadsheet ID |
| 2 | Create Drive folder "Property Uploads" | Folder ID |
| 3 | Paste code in Apps Script `Code.gs` + add IDs | Backend code ready |
| 4 | Paste `appsscript.json` settings | Timezone + web access set |
| 5 | Run `setupProject` | Sheet headers created |
| 6 | Deploy as Web App (Anyone) | Web App URL |
| 7 | Paste URL in `script.js` | Website connected |
| 8 | Open site in browser | Landing page works |
| 9 | Submit test property | Row in Sheet + files in Drive |
| 10 | Upload to GitHub (3 frontend files only) | Code backed up safely |

---

# Your Current Setup (Already Done)

If you followed along, your project is already configured:

| Item | Status |
|------|--------|
| Google Sheet | ✅ Created |
| Drive folder "Property Uploads" | ✅ Created |
| Apps Script `setupProject` | ✅ Passed |
| Web App deployed | ✅ Live |
| `script.js` connected | ✅ Configured |

**Your live API test:**
Open your Web App URL in a browser — you should see:
```json
{"success":true,"message":"Property submission API is running.","version":"1.0.0"}
```

**Next steps for you:**
1. Test a full form submission at `http://localhost/New%20folder/`
2. Push safe files to GitHub (Step 10–11)
3. Deploy frontend to a live host when ready

---

*Need help? Check the Troubleshooting section above or review the Executions log in Google Apps Script.*
