# PhishShield: Real-time Phishing URL Detector

What this is:
- A Chrome extension + local Python server that detect phishing in real-time.
- Color-coded risk, hover tips on links, auto-block dangerous pages, and “Go back/Proceed” actions.
- Uses ML (scikit-learn), VirusTotal, WHOIS, and OpenPhish feed.

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
