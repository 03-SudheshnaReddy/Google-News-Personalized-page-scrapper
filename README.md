# ⚠️ THIS PROJECT IS FOR LEARNING PURPOSES ONLY AND DOES NOT INTEND TO MISUSE ANY GOOGLE SERVICES OR DATA

# 📰 Google News Personalized Scraper (Automated)

## 🚀 Project Overview and goal
I built this project to **fully automate scraping of the **Google News "For You" page** for multiple users and store personalized news data into Supabase Database**.

The system is designed to:
- Scrape news for **5 different users (separate sessions/emails)**
- Extract **top 4 topics per user (treated as tabs)**
- Extract **~4 articles per topic → ~16 articles per user**
- Run automatically **every 6 hours** using GitHub Actions
- Store structured data for analysis and ML use

---

## ⚙️ Execution Flow (Automation Pipeline)

1. GitHub Actions Trigger  
   ↓  
2. Run Workflow  
   ↓  
3. Execute Python Script (`app.py`)  
   ↓  
4. Loop through 5 users  
   ↓  
5. Scrape topics + articles  
   ↓  
6. Store in Supabase  

---


## 🧠 Core Logic (Step-by-Step Flow)

For every scheduled run:

1. Load all **5 user sessions**
2. For each user:
   - Open Google News "For You"
   - Extract **top 4 topics (tabs)**
   - For each topic:
     - Extract ~4 articles
3. Store all extracted data in Supabase

📊 Output per run:
- 5 users × 16 articles ≈ **80 articles per run**

---

## 🧪 Data Collection Plan

I will run this pipeline for:

📅 **7 days**

To collect:

- User-topic interaction data  
- Article frequency  
- Trend changes over time  

---

## ⏱️ Automation Strategy

Initially, I considered:

### ❌ Option 1: Google Cloud
- Not fully free
- Requires billing setup

### ❌ Option 2: Manual execution
- Run script every 6 hours on laptop
- Easy setup
- But impractical to maintain daily

### ✅ Final Choice: GitHub Actions

I used GitHub Actions because:
- Free tier available
- Easy cron-based scheduling
- Runs automatically in cloud
- No need to keep system ON

---

## 🔄 GitHub Actions Scheduling

```yaml
on:
  schedule:
    - cron: "0 */6 * * *"
 ```

## 🔐 Persistent Login (Why I Used It)

Google News "For You" is **personalized**.

So I used **persistent login sessions** instead of logging in every time.

### Why?
- Avoid repeated login requests
- Maintain user-specific personalization
- Reduce chances of bot detection
- Faster execution

Sessions are stored securely and reused during scraping.

---

## ⚠️ Why Dummy Emails / Multiple Accounts Are Used

I used multiple accounts (dummy/test emails) for:

- Simulating **different user preferences**
- Avoiding **rate limits / blocking**
- Distributing scraping load

### Must follow Precautions:
- Do NOT over-request data
- Keep scraping intervals reasonable (6 hours ✔)
- Avoid aggressive automation
- Never expose session data publicly

---



## 📷 Database Schema

<img width="1796" height="539" alt="image" src="https://github.com/user-attachments/assets/7e8076fb-d1b0-42ba-8db6-4944535e5489" />


---

## 👩‍💻 Author

Sudheshna Reddy


