PhishShield: Real-time Phishing URL Detector

Team Name:- Syntax Slayers
Team members:-
1.) Gagan Chaudhary
2.)J.Tirupati Rao
3.)Samiksha Verma
4.)Nomita Hirwani

Problem Statement:- Develop a browser extension or app that can detect and block phishing websites in real-time, with a scoring system and visual alert for the user.

Suggested Tech Stack:

ML (Scikit-learn), React/Vanilla JS for extension, VirusTotal API, Flask backend

What this is:
- A Chrome extension + local Python server that detect phishing in real-time.
- Color-coded risk, hover tips on links, auto-block dangerous pages, and “Go back/Proceed” actions.
- Uses ML (scikit-learn), VirusTotal, WHOIS, and OpenPhish feed.

Future Scope of PhishShield

Smarter Detection

Upgrade to deep learning & NLP for better accuracy.

Add image/logo similarity checks to catch brand spoofing.

Scalability & Reach

Cloud-based threat intelligence sharing.

Multi-browser & mobile support.

User-Centric Features

Explainable alerts (“Domain too new”, “Suspicious forms”).

Training/simulation mode for user awareness.

Integrations

Email clients & password managers.

Enterprise APIs & SIEM/SOC integration.

Advanced Research

Resilience against adversarial attacks.

Zero-day phishing detection improvements.

Tech Stack Used

This document lists the technologies, libraries, and tools used in the development of the PhishShield program.

---

## Frontend (Chrome Extension)
The frontend is a Chrome extension that interacts with the backend to provide real-time phishing detection.

### Technologies Used:
- **HTML5**: For structuring the extension's user interface (e.g., `popup.html`, `block.html`).
- **CSS3**: For styling the extension's UI (e.g., `banner.css`, `block.css`, `hover.css`, `popup.css`).
- **JavaScript (ES6)**: For implementing the extension's logic and interactivity (e.g., `background.js`, `content.js`, `popup.js`, `block.js`).
- **Chrome Extensions API**: For interacting with the browser (e.g., `chrome.runtime`, `chrome.storage`, `chrome.webNavigation`).
- **Manifest v3**: For defining the extension's metadata and permissions (`manifest.json`).

### Assets:
- **Icons**: PNG images for the extension's icons (`assets/icon-16.png`, `assets/icon-48.png`, `assets/icon-128.png`).

---

## Backend (Python Server)
The backend is a Python server that performs phishing detection using machine learning, heuristics, and external APIs.

### Technologies Used:
- **Python 3.10+**: The primary programming language for the backend.
- **Flask**: A lightweight web framework for building the backend API (`app.py`).
- **Flask-CORS**: For enabling Cross-Origin Resource Sharing (CORS) in the backend.
- **scikit-learn**: For training and using machine learning models (`train_model.py`).
- **NumPy**: For numerical computations (`features.py`, `train_model.py`).
- **Pandas**: For data manipulation and analysis (`train_model.py`).
- **Joblib**: For saving and loading machine learning models (`train_model.py`).
- **Requests**: For making HTTP requests to external services (`vt.py`, `feeds.py`).
- **dotenv**: For managing environment variables (`config.example.env`).


---

## Data and Machine Learning
- **Dataset**: A CSV file containing labeled URLs for training the machine learning model (`data/dataset.csv`). Dataset downloaded from Kaggle(phishing_site_urls.csv)
- **Gradient Boosting Classifier**: A machine learning algorithm used for phishing detection (`train_model.py`).
- **Feature Engineering**: Custom features extracted from URLs, domains, and page content (`features.py`).

---

## General Tools and Libraries
- **Requirements File**: Dependencies are listed in `requirements.txt` for easy installation.
---

## Development and Deployment
- **Google Chrome**: The target browser for the extension.
- **Command Line Interface (CLI)**: For running scripts like `train_model.py` and `app.py`.

---

## Folder Structure
- **Frontend**: Contains the Chrome extension files.
- **Backend**: Contains the Python server files, dataset, and machine learning model.

---

This program combines modern web technologies, machine learning, and external APIs to provide a robust phishing detection system.

Quick Start (super easy):
1) Train model (backend/README.md) or use the heuristics first.
2) Start backend: python app.py (inside phishshield/backend)
3) Load extension: chrome://extensions -> Developer Mode -> Load unpacked -> phishshield/frontend
4) Visit websites and watch PhishShield protect you.

Project Flow:
- Extension asks backend for a score.
- Backend runs:
  - Fast URL checks (length, dots, hyphens, entropy, special chars, IP-in-URL).
  - Domain checks (WHOIS, domain age).
  - Page content signals (keywords, forms, scripts).
  - VirusTotal reputation (optional).
  - OpenPhish blacklist.
- Backend returns 0..100 score. Extension shows:
  - Green <= 30: Safe
  - Yellow 31..65: Suspicious
  - Red >= 80: Dangerous (auto-block)

# PhishShield (Frontend - Chrome Extension)

Hello! This is the "face" of our project — the part you see in Chrome.

What it does:
- Shows a colored banner on websites (Green = Safe, Yellow = Suspicious, Red = Dangerous).
- Blocks very dangerous sites with a big warning page.
- Shows a small score bubble when you hover links, so you know if a link is safe.
- Shows a popup when you click the extension icon with more details and buttons.

How to install it (like teaching a kid):
1) Open Google Chrome.
2) In the top bar, type: chrome://extensions and press Enter.
3) Turn ON "Developer mode" (top right).
4) Click the button "Load unpacked".
5) Find and select the folder: phishshield/frontend
6) You will now see PhishShield appear in your extensions list (with a shield icon).

How to use:
- Start the backend server first (see backend/README.md).
- Visit any website. A tiny banner at the top will say "Safe", "Suspicious", or "Dangerous".
- Hover over any link to see a small safety score tooltip.
- If a site is dangerous, PhishShield will block it. You can choose to "Proceed anyway" (not recommended) or "Go back".
- Click the extension icon to open the popup showing the score and reasons.

Notes:
- The extension connects to a local server at http://127.0.0.1:5000
- If the server is off, you'll see "Backend unavailable" in reasons.
- You can whitelist a site by clicking "Proceed anyway" so PhishShield will stop warning you for that domain.


# PhishShield (Backend - Local Server)

Hello! This is the "brain" of PhishShield. It:
- Learns from examples (your dataset) what phishing URLs look like.
- Checks links and pages for danger signs.
- Talks to the Chrome extension and gives it a score.

You will:
- Put your dataset file here.
- Train the ML model (one command).
- Start the server (one command).

Step 0: Install Python
- Make sure Python 3.10 or newer is installed.
- On Windows, you can check by opening "CMD" and typing: python --version

Step 1: Install things the server needs
- Open CMD in this folder (phishshield/backend)
- Type this and press Enter:
  pip install -r requirements.txt

Step 2: Put your dataset file
- Put your dataset CSV file here: phishshield/backend/data/dataset.csv
- The file must have columns: url,label
- The label can be 1/0 or phishing/legit (we handle both)

Example of 3 lines:
url,label
http://paypal.com,0
http://pa-ypal.com.verify-user-login.co,1
http://google.com,0

Step 3: Train the model
- In CMD (in phishshield/backend), run:
  python train_model.py --data data/dataset.csv

What happens:
- The script reads your CSV, learns URL patterns, and saves a model file: model.pkl
- It also prints accuracy so you see how good it is.

Step 4: Set VirusTotal (optional, but nice)
- Get a free API key from virustotal.com (create a free account).
- Create a file named .env in this folder (phishshield/backend) and put:
  VT_API_KEY=your_virustotal_key_here
- If you skip this, it still works, just without VirusTotal.

Step 5: Start the server
- In CMD (phishshield/backend), run:
  python app.py
- You should see: "PhishShield backend running at http://127.0.0.1:5000"

Step 6: Use the extension
- Make sure Chrome extension is loaded (see frontend/README.md).
- Visit websites and see PhishShield in action!

How it works inside (simple words):
- Fast checks (URL level): We look for weird symbols, too many dots, IP addresses, strange words like "verify" in the URL. Fast and instant.
- Domain checks (WHOIS): We check how old the domain is. Very new domains are often bad.
- Content checks: We look for "login/password" forms where they shouldn't be, scary words like "urgent", and sneaky scripts.
- VirusTotal check: If you gave an API key, we ask VirusTotal if the domain/URL is known to be bad.
- Blacklist: We download OpenPhish (public phishing feed) to block known bad sites.

Scoring rules:
- We mix ML prediction + rules + VirusTotal + blacklist = final score (0 to 100)
- 0-30: safe (green)
- 31-65: suspicious (yellow)
- 80-100: dangerous (red, usually blocked)

Need help?
- If something fails, read messages in CMD. Most errors will say exactly what is wrong.
