# 🚀 AI-Powered Lead Enrichment Pipeline

### **Overview**

A hybrid automation tool designed to streamline the data verification process for high-volume sales research. This system integrates **Computer Vision (Google Gemini AI)** with **Selenium Web Automation** to autonomously extract, verify, and deduplicate contact information (Mobiles, Directs, Emails) from multiple data enrichment platforms (Lusha, Cognism, ZoomInfo).

The tool acts as a bridge between raw LinkedIn URLs and a structured Google Sheets database, allowing the research team to enrich leads in real-time with zero manual data entry.

---

### 🔍 The Problem

* **Manual Fatigue:** The research team was manually toggling between LinkedIn, Lusha, and Excel, taking 3-5 minutes per lead.
* **Data Inconsistency:** Manual copying led to duplicate phone numbers and formatting errors.
* **Missing Data:** If one tool (e.g., Lusha) lacked data, the researcher often skipped cross-checking others due to time constraints.

### 💡 The Solution

I engineered a **dashboard-controlled bot** that listens for input in a Google Sheet. When a user pastes a LinkedIn URL:

1. **Browser Orchestration:** The bot autonomously navigates to the profile and activates enrichment extensions.
2. **Visual AI Analysis:** Instead of relying solely on brittle HTML scrapers, the bot takes a **screenshot** of the extension sidebar.
3. **Data Extraction:** Google Gemini (Generative AI) parses the image to extract names, emails, and phone numbers, identifying context (e.g., differentiating "Work" vs "Mobile").
4. **HTML Fallback:** For complex elements like truncated Job Titles, a targeted Selenium scraper injects into the DOM to retrieve full text.
5. **Intelligent Logic:**
* *Auto-Translation:* Detects foreign job titles (e.g., "Chef De Projet") and translates them to English.
* *De-Duplication:* Compares extracted numbers against a "Seen" set to remove duplicates between tools.
* *Waterfall Enrichment:* If Phase 1 (Lusha) yields no results, it automatically triggers Phase 2 (ZoomInfo) protocol.



---

### 🛠 Tech Stack

* **Language:** Python 3.10+
* **Browser Automation:** Selenium WebDriver (Chrome)
* **Artificial Intelligence:** Google Gemini 2.0 Flash API (Vision & Text Processing)
* **Database/UI:** Google Sheets API (`gspread`), Google Apps Script (Trigger Management)
* **Authentication:** OAuth2 Service Accounts

---

### 🔄 System Architecture

1. **Input Layer:** Google Apps Script detects user activity (User Email + LinkedIn URL) and flags the system status as `INITIALIZING`.
2. **Processing Layer (Local Host):**
* Sanitizes URL inputs.
* Scrapes raw HTML for metadata (Designation/Company).
* Captures viewport screenshots for OCR/AI analysis.


3. **Intelligence Layer:**
* Gemini API receives the screenshot + Raw HTML Context.
* Returns a structured JSON object containing verified contact details.


4. **Storage Layer:**
* Python parses the JSON.
* Applies regex normalization to phone numbers.
* Writes clean, formatted rows back to the Master Google Sheet.



---

### 📈 Impact

* **Efficiency:** Reduced processing time from ~4 mins to **~30 seconds per lead**.
* **Accuracy:** Eliminated copy-paste errors and duplicate entries via Python-based set logic.
* **Coverage:** Increased data fill rate by automatically cascading to secondary tools (ZoomInfo) when primary tools failed.

---

### 💻 Code Snippet (Logic Preview)

*Note: Full source code is proprietary. Below is a sanitized example of the Hybrid (HTML + Vision) logic used.*

```python
def analyze_data_hybrid(driver, image_path):
    # 1. Scrape specific HTML elements that are often truncated visually
    try:
        raw_title = driver.find_element(By.CSS_SELECTOR, "div[job-title]").text
    except:
        raw_title = "Unknown"

    # 2. Pass both Image and HTML context to AI for final synthesis
    prompt = f"""
    Analyze this screenshot. 
    Context from HTML: The raw job title is '{raw_title}'.
    Task: Extract contact details and TRANSLATE the job title to English.
    Return JSON format.
    """
    
    response = gemini_client.generate_content([prompt, image_path])
    return json.loads(response.text)

```

---

### 🚀 Future Improvements

* **Headless Mode:** Deploying to a cloud server (AWS/GCP) to run without a visible browser UI.
* **Batch Processing:** enabling the upload of CSV files for bulk enrichment overnight.
