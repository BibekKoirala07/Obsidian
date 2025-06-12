### ✅ Day 1: Setup & First Automation

- 🔧 Install Playwright
    
- 🔄 Record your first script using `codegen`
    
- 🧠 Learn:
    
    - `page.goto()`, `page.click()`, `page.fill()`
        
    - `await page.waitForSelector()`
        
    - Headless vs headed
        

**Project**: Automate login on a simple demo site

---

### ✅ Day 2: Waits, Assertions, and Locators

- 📍 Learn smart locators (`getByRole`, `getByText`, `nth()`)
    
- 🕰 Master automatic waits (`waitForSelector`, `waitForNavigation`)
    
- ✅ Use `expect()` for checks (Playwright Test runner)
    

**Project**: Automate a 2-step form

---

### ✅ Day 3: Handle OTP Input UI (Static OTP)

- 🧩 Target the OTP input field(s)
    
- 🧪 Automate filling static OTPs
    
- ⏱ Add delays, handle slow React components
    

**Project**: Script login using fake/static OTP

---

### ✅ Day 4: Fetch OTP from Email

- 📧 Use IMAP + `mailparser` (Node.js) or Gmail API
    
- 🧠 Use regex to extract OTP
    
- 🔒 Handle Gmail OAuth or use test inbox (MailSlurp, Mailinator)
    

**Project**: Build a Node function `getOtpFromEmail()`

---

### ✅ Day 5: Combine UI + Email OTP

- 🔗 Combine Playwright with OTP email fetch logic
    
- 🔁 Retry logic if OTP hasn't arrived yet
    
- 🧪 Use `Promise.race` or `setTimeout` for polling
    

**Project**: Full login bot — open site → request OTP → fetch email → type OTP → log in

---

### ✅ Day 6: Handle Errors, Sessions, and Captchas

- 🧱 Add try/catch, logs, screenshot on error
    
- 🍪 Save storage state for future reuse (skip OTP)
    
- 🔐 Handle captchas (optional)
    
- 📁 File download/upload examples
    

---

### ✅ Day 7: Polish & Deploy

- 🧪 Wrap logic in CLI with `yargs` or `commander`
    
- ⚙️ Add `.env`, config system
    
- 🐳 Optional: Dockerize it
    
- 🕐 Optional: Run on schedule (CRON, GitHub Actions, etc.)
    

**Deliverable**: Production-ready CLI tool that logs into your friend's site reliably

---

## 🧠 Bonus Career Tips

- 📂 Add a “Playwright Automation” section to your GitHub with this bot
    
- 💬 Share a blog post or tweet: “Built an OTP-login automation bot with Playwright — here's how”
    
- 💼 Mention Playwright in interviews — it's hot for testing & automation engineer roles
    

---

## ✅ Starter Tech Stack for the Project

- `playwright` (JS or TS)
    
- `dotenv` for secrets
    
- `imap-simple` or `gmail-api` for OTP
    
- Optional: `yargs`, `commander`, `chalk` for CLI
    
- Optional: Docker + GitHub Actions