# 📰 Google News Personalized Scraper

> **Disclaimer:**
> This project is built **strictly for learning and research purposes only**. It does not intend to misuse Google services, bypass platform restrictions, or expose any user/session data publicly.

---

## 🚀 Project Overview

This project automates the collection of personalized Google News articles from the **Google News “For You”** page for multiple test users and stores the extracted structured data into a **Supabase database**.

The main goal of this project is to study how personalized news feeds differ across multiple users and how article topics change over time.

The system is designed to:

* Scrape personalized news for multiple users using separate saved sessions.
* Extract top topics shown on the Google News “For You” page.
* Extract articles under each topic.
* Store structured article data in Supabase.
* Run automatically every 6 hours using GitHub Actions.
* Collect data for analysis and possible ML-based personalization study.

---

## 🎯 Project Goal

The goal is to collect personalized news data for different user sessions and analyze:

* User-specific topic recommendations
* Article frequency across users
* Topic trends over time
* Differences between personalized feeds
* News recommendation behavior over multiple scheduled runs

---

## ⚙️ High-Level Automation Flow

```text
GitHub Actions Trigger
        ↓
Run Scheduled Workflow
        ↓
Execute Python Scraper
        ↓
Load Saved User Sessions
        ↓
Open Google News "For You"
        ↓
Extract Topics and Articles
        ↓
Store Data in Supabase
```

---

## 🧠 Core Logic

For every scheduled run:

1. Load saved session files for all users.
2. For each user:

   * Open Google News using saved login session.
   * Navigate to the personalized “For You” page.
   * Extract the top topics/tabs shown for that user.
3. For each topic:

   * Extract articles under that topic.
   * Store article title, link, source, topic, timestamp, and user/session identifier.
4. Save all extracted data into Supabase.

---

## 📊 Expected Output Per Run

Example setup:

```text
5 users × 4 topics × 4 articles ≈ 80 articles per run
```

Since the workflow is scheduled every 6 hours:

```text
4 runs per day × 80 articles ≈ 320 articles per day
```

For a 7-day pilot:

```text
320 articles/day × 7 days ≈ 2240 articles
```

Actual output may vary because Google News content is dynamic and may not always return the same number of topics/articles.

---

## 🧪 Data Collection Plan

The planned pilot run duration is:

```text
7 days
```

During this period, the system collects:

* User-wise personalized topics
* Article titles
* Article URLs
* News sources
* Topic/category names
* Collection timestamp
* User/session identifier

This data can later be used for:

* Trend analysis
* User-feed comparison
* Topic frequency analysis
* Personalized recommendation study
* ML-based article ranking experiments

---

## 🔐 Persistent Login: Why It Is Used

Google News “For You” is personalized and usually depends on a logged-in user session.

Repeated automated logins are risky because they may trigger:

* CAPTCHA challenges
* Session invalidation
* Account verification prompts
* Temporary account restrictions
* IP-based rate limiting

To reduce repeated login attempts, the project uses **persistent login sessions**.

This means:

```text
Login manually once → Save browser session → Reuse session for scraping
```

The user logs in manually only once. After login, the browser session is saved and reused later by the scraper.

---

## 🧾 Step 1: Create Login Sessions

The session is created using `1_login.py`.

Example command:

```bash
python 1_login.py --user user1@gmail.com
```

The script opens a browser window where the user manually completes Google login.

After successful login, the session is saved as a JSON file inside the `sessions/` folder.

Example:

```text
sessions/user1.json
sessions/user2.json
sessions/user3.json
```

These JSON files contain browser storage state such as:

* Cookies
* Local storage
* Authentication/session data

This allows the scraper to reuse the login session later without entering credentials again.

---

## 📁 Step 2: Session Storage

The saved session JSON files are stored inside:

```text
sessions/
```

Example structure:

```text
project-root/
│
├── 1_login.py
├── 2_pilot_scraper.py
├── sessions/
│   ├── user1.json
│   ├── user2.json
│   └── user3.json
└── session_b64.txt
```

The session may remain valid for days or weeks, but this is not guaranteed. It depends on Google’s session expiry, security checks, IP changes, and login activity.

---

## 🔑 Step 3: Base64 Encoding and GitHub Secrets

The session JSON files are converted into **Base64-encoded text** before storing them in GitHub Secrets.

Important correction:

```text
Base64 is NOT "64-bit".
```

Base64 is a text encoding format used to safely store JSON/session content inside environment variables or GitHub Secrets.

Example:

```text
session_b64.txt
```

This file contains the encoded version of the session JSON.

The encoded session value is then added to GitHub Secrets so that GitHub Actions can access it securely during workflow execution.

---

## ▶️ Step 4: Run the Scraper

After sessions are created, run the scraper file:

```bash
python 2_pilot_scraper.py
```

The scraper:

* Loads saved session data.
* Opens Google News using the authenticated session.
* Accesses the personalized “For You” page.
* Extracts topics and articles.
* Stores the scraped data into Supabase.

Because the saved session is reused, the user does not need to log in manually during every run.

---

## 🗄️ Step 5: Store Data in Supabase

The extracted article data is stored in Supabase.

The database stores structured information such as:

* User/session ID
* Topic name
* Article title
* Article URL
* Source name
* Scraped timestamp
* Run ID/date

---

## 📷 Database Schema

<img width="1796" height="539" alt="image" src="https://github.com/user-attachments/assets/7e8076fb-d1b0-42ba-8db6-4944535e5489" />

---

## ⏱️ Automation Strategy

To run the scraper every 6 hours, three options were considered.

---

### ❌ Option 1: Manual Laptop Execution

This means keeping a laptop running continuously and manually running the scraper every 6 hours.

Problems:

* Laptop must stay on 24/7.
* Internet must always be available.
* Manual monitoring is required.
* Not reliable for multi-day collection.

So this option was not practical.

---

### ❌ Option 2: Cloud Virtual Machine

A cloud VM such as Google Cloud can run the scraper continuously.

Advantages:

* More stable than local laptop.
* Can run 24/7.
* Better for automation.

Limitations:

* May require billing setup.
* Can introduce infrastructure cost.
* Needs cloud configuration and maintenance.

---

### ✅ Option 3: GitHub Actions

GitHub Actions was selected because:

* It has a free tier.
* It supports cron-based scheduling.
* No need to keep a laptop running.
* Secrets can be stored securely.
* Workflow can run automatically every 6 hours.

Example schedule:

```yaml
on:
  schedule:
    - cron: "0 */6 * * *"
```

This runs the scraper once every 6 hours.

---

## 🔄 GitHub Actions Flow

```text
Scheduled Cron Trigger
        ↓
GitHub Runner Starts
        ↓
Load GitHub Secrets
        ↓
Decode Base64 Session Data
        ↓
Recreate Session JSON
        ↓
Run Python Scraper
        ↓
Scrape Google News
        ↓
Store Results in Supabase
```

---

## ⚠️ Current Challenge

The main challenge is that Google News “For You” is highly personalized and session-sensitive.

A session created locally may not behave reliably inside GitHub Actions because GitHub Actions runs on a fresh remote machine each time.

The GitHub-hosted runner may have:

* Different IP address
* Different browser fingerprint
* Different machine environment
* No previous browsing history
* Fresh browser context

Because of this, Google may not always load the personalized “For You” feed correctly.

In some runs:

* Authentication may work.
* Google News may open.
* But personalized content may not fully load.
* The scraper may return `0 articles`.

So the main issue is not only the code. The issue is that Google News personalization depends heavily on session, device, IP, and browser environment.

---

## ⚠️ Precautions

To keep the project safe and responsible:

* Do not expose session JSON files publicly.
* Do not commit session files to GitHub.
* Store session values only in GitHub Secrets.
* Do not run scraping too frequently.
* Keep reasonable intervals such as 6 hours.
* Do not collect private user data.
* Use only test/dummy accounts where permitted.
* Do not use this project for commercial misuse or unauthorized data collection.

---

## 📌 Important Notes

* GitHub Actions is CI automation, not a full persistent cloud machine.
* `session_b64.txt` contains Base64-encoded session text, not 64-bit data.
* Session files may remain valid for days or weeks, but there is no fixed guarantee.
* Persistent login reduces repeated login attempts but does not guarantee scraping reliability.
* Google News personalized feeds may behave differently across local and cloud environments.

---

## 🧾 Project Summary

This project is a multi-user Google News personalized scraper built for learning and research purposes.

It uses persistent login sessions to access personalized Google News feeds, extracts topics and articles for multiple users, stores the data in Supabase, and attempts scheduled automation using GitHub Actions.

The key technical challenge is that Google News “For You” is personalized and session-sensitive, so sessions created locally do not always work reliably on GitHub-hosted runners.

---

## 👩‍💻 Author

**Sudheshna Reddy**






