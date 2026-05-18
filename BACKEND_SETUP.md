# Backend Setup Guide 🚀

Complete guide for setting up a secure backend server to handle SWAT Deployment Log submissions from multiple users.

## 📋 Table of Contents

1. [Architecture Overview](#architecture-overview)
2. [Quick Start (Node.js/Express)](#quick-start-nodejs)
3. [Python/Flask Alternative](#python-flask-alternative)
4. [Deployment Options](#deployment-options)
5. [Security Best Practices](#security-best-practices)
6. [Troubleshooting](#troubleshooting)

---

## 🏗️ Architecture Overview

```
┌─────────────────────────────────────────────────────────────────┐
│                        FRONTEND (User)                          │
│  - swat-deployment-log.html (no secrets exposed)               │
│  - Submits form data to backend via /api/swat/log             │
└────────────────────────┬────────────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────────────┐
│                    YOUR BACKEND SERVER                          │
│  ✅ Stores Discord webhook URL (secret)                       │
│  ✅ Validates user input (XSS/injection prevention)           │
│  ✅ Rate limits submissions                                   │
│  ✅ Logs audit trail                                          │
│  ✅ Handles errors gracefully                                 │
└────────────────────────┬────────────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────────────┐
│                      DISCORD WEBHOOK                            │
│  - Posts formatted embeds to your Discord server              │
└─────────────────────────────────────────────────────────────────┘
```

**Key Benefits:**
- Webhook URL never exposed to client
- Multiple concurrent users supported
- Server-side validation & security
- Rate limiting & abuse prevention
- Audit trail of all submissions

---

## 🚀 Quick Start (Node.js/Express)

### Step 1: Install Node.js

Download from https://nodejs.org/ (LTS version recommended)

### Step 2: Create Project Directory

```bash
mkdir swat-backend
cd swat-backend
npm init -y
```

### Step 3: Install Dependencies

```bash
npm install express dotenv cors body-parser
```

### Step 4: Create `.env` File

Create a `.env` file in your project root:

```env
DISCORD_WEBHOOK_URL=https://discord.com/api/webhooks/YOUR_WEBHOOK_ID/YOUR_WEBHOOK_TOKEN
PORT=3000
RATE_LIMIT_WINDOW=60000
RATE_LIMIT_MAX_REQUESTS=10
```

**Get your webhook URL:**
1. Go to your Discord server
2. Settings → Webhooks → Create Webhook
3. Copy the URL
4. Paste it in `.env`

### Step 5: Create `server.js`

```javascript
const express = require('express');
const cors = require('cors');
const bodyParser = require('body-parser');
require('dotenv').config();

const app = express();

// Middleware
app.use(cors());
app.use(bodyParser.json());

// Rate limiting (simple in-memory)
const submissions = {};

function checkRateLimit(ip) {
    const now = Date.now();
    const windowMs = process.env.RATE_LIMIT_WINDOW || 60000; // 1 min default
    const maxRequests = process.env.RATE_LIMIT_MAX_REQUESTS || 10;

    if (!submissions[ip]) {
        submissions[ip] = [];
    }

    // Remove old submissions outside window
    submissions[ip] = submissions[ip].filter(time => now - time < windowMs);

    if (submissions[ip].length >= maxRequests) {
        return false; // Rate limited
    }

    submissions[ip].push(now);
    return true;
}

// Main endpoint
app.post('/api/swat/log', async (req, res) => {
    try {
        // Rate limiting
        const ip = req.ip;
        if (!checkRateLimit(ip)) {
            return res.status(429).json({ 
                message: 'Too many requests. Please wait before submitting again.' 
            });
        }

        // Validate input
        const { title, narrative, roster } = req.body;

        if (!title || !narrative || !roster) {
            return res.status(400).json({ 
                message: 'Missing required fields: title, narrative, roster' 
            });
        }

        // Sanitize input (prevent injection)
        const sanitize = (str) => {
            return str
                .replace(/[&<>"']/g, (match) => {
                    const escape = {
                        '&': '&amp;',
                        '<': '&lt;',
                        '>': '&gt;',
                        '"': '&quot;',
                        "'": '&#39;'
                    };
                    return escape[match];
                })
                .substring(0, 2000); // Limit length
        };

        const safeTitle = sanitize(title);
        const safeNarrative = sanitize(narrative);
        const safeRoster = sanitize(roster);

        // Build Discord payload
        const discordPayload = {
            embeds: [{
                title: `🚨 DEPLOYMENT LOG: ${safeTitle}`,
                color: 15747399, // Tactical red
                fields: [
                    {
                        name: "👥 Personnel Roster & Status",
                        value: safeRoster,
                        inline: false
                    },
                    {
                        name: "📝 Operation Narrative",
                        value: safeNarrative,
                        inline: false
                    }
                ],
                timestamp: new Date().toISOString(),
                footer: {
                    text: "SWAT Automated Logging System"
                }
            }]
        };

        // Send to Discord
        const response = await fetch(process.env.DISCORD_WEBHOOK_URL, {
            method: 'POST',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify(discordPayload)
        });

        if (!response.ok) {
            console.error('Discord error:', await response.text());
            return res.status(500).json({ 
                message: 'Failed to post to Discord' 
            });
        }

        // Success
        console.log(`[${new Date().toISOString()}] Submission from ${ip}: ${safeTitle}`);
        res.status(200).json({ 
            message: 'Log successfully posted to Discord' 
        });

    } catch (error) {
        console.error('Server error:', error);
        res.status(500).json({ 
            message: 'Internal server error' 
        });
    }
});

// Health check endpoint
app.get('/api/health', (req, res) => {
    res.json({ status: 'OK' });
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
    console.log(`🚀 SWAT Backend running on http://localhost:${PORT}`);
    console.log('📝 Submission endpoint: POST /api/swat/log');
});
```

### Step 6: Run the Server

```bash
node server.js
```

You should see:
```
🚀 SWAT Backend running on http://localhost:3000
📝 Submission endpoint: POST /api/swat/log
```

### Step 7: Update Frontend

In `swat-deployment-log.html`, update the `BACKEND_URL`:

```javascript
const BACKEND_URL = "http://localhost:3000/api/swat/log"; // Development
// const BACKEND_URL = "https://your-domain.com/api/swat/log"; // Production
```

### Step 8: Test Locally

1. Open `swat-deployment-log.html` in your browser
2. Fill in the form
3. Click "Submit Log to Discord"
4. Check your Discord channel

---

## 🐍 Python/Flask Alternative

If you prefer Python:

### Step 1: Install Python & Dependencies

```bash
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
pip install flask python-dotenv requests flask-cors
```

### Step 2: Create `.env`

```env
DISCORD_WEBHOOK_URL=https://discord.com/api/webhooks/YOUR_WEBHOOK_ID/YOUR_WEBHOOK_TOKEN
PORT=3000
```

### Step 3: Create `app.py`

```python
from flask import Flask, request, jsonify
from flask_cors import CORS
import requests
import os
from dotenv import load_dotenv
from datetime import datetime
import html

load_dotenv()

app = Flask(__name__)
CORS(app)

submissions = {}

def check_rate_limit(ip, window_ms=60000, max_requests=10):
    now = datetime.now().timestamp() * 1000
    if ip not in submissions:
        submissions[ip] = []
    
    submissions[ip] = [t for t in submissions[ip] if now - t < window_ms]
    
    if len(submissions[ip]) >= max_requests:
        return False
    
    submissions[ip].append(now)
    return True

def sanitize(text, max_length=2000):
    return html.escape(str(text))[:max_length]

@app.route('/api/swat/log', methods=['POST'])
def swat_log():
    try:
        if not check_rate_limit(request.remote_addr):
            return jsonify({'message': 'Too many requests'}), 429
        
        data = request.get_json()
        title = data.get('title')
        narrative = data.get('narrative')
        roster = data.get('roster')
        
        if not all([title, narrative, roster]):
            return jsonify({'message': 'Missing required fields'}), 400
        
        title = sanitize(title)
        narrative = sanitize(narrative)
        roster = sanitize(roster)
        
        payload = {
            'embeds': [{
                'title': f'🚨 DEPLOYMENT LOG: {title}',
                'color': 15747399,
                'fields': [
                    {'name': '👥 Personnel Roster & Status', 'value': roster, 'inline': False},
                    {'name': '📝 Operation Narrative', 'value': narrative, 'inline': False}
                ],
                'timestamp': datetime.now().isoformat() + 'Z',
                'footer': {'text': 'SWAT Automated Logging System'}
            }]
        }
        
        response = requests.post(os.getenv('DISCORD_WEBHOOK_URL'), json=payload)
        
        if response.status_code != 204:
            return jsonify({'message': 'Failed to post to Discord'}), 500
        
        print(f'[{datetime.now()}] Submission from {request.remote_addr}: {title}')
        return jsonify({'message': 'Log successfully posted to Discord'}), 200
        
    except Exception as e:
        print(f'Error: {e}')
        return jsonify({'message': 'Internal server error'}), 500

@app.route('/api/health', methods=['GET'])
def health():
    return jsonify({'status': 'OK'})

if __name__ == '__main__':
    port = int(os.getenv('PORT', 3000))
    app.run(debug=True, port=port)
```

### Step 4: Run

```bash
python app.py
```

---

## 🌐 Deployment Options

### Option 1: Heroku (Easiest)

```bash
# Install Heroku CLI
# Login
heroku login

# Create app
heroku create swat-backend

# Set environment variable
heroku config:set DISCORD_WEBHOOK_URL="your_webhook_url"

# Deploy
git push heroku main
```

### Option 2: Vercel (Serverless)

```bash
npm install -g vercel
vercel login
vercel deploy
```

Create `vercel.json`:
```json
{
  "buildCommand": "npm install",
  "env": {
    "DISCORD_WEBHOOK_URL": "@discord_webhook_url"
  }
}
```

### Option 3: DigitalOcean (VPS)

1. Create Droplet (Ubuntu 22.04, $5/month)
2. SSH into server
3. Install Node.js
4. Clone your repo
5. Install dependencies
6. Use PM2 for process management:

```bash
npm install -g pm2
pm2 start server.js --name "swat-backend"
pm2 startup
pm2 save
```

---

## 🔐 Security Best Practices

### 1. Environment Variables
- **Never commit** `.env` to Git
- Add `.env` to `.gitignore`
- Use secrets management for production

### 2. Input Validation
- ✅ Sanitize all user input
- ✅ Enforce length limits
- ✅ Validate data types

### 3. Rate Limiting
- ✅ Prevent spam/DDoS
- ✅ Implement per-IP limits
- ✅ Use temporary bans for abuse

### 4. HTTPS/TLS
- ✅ Always use HTTPS in production
- ✅ Use SSL certificates (Let's Encrypt free)
- ✅ Redirect HTTP to HTTPS

### 5. Webhook URL Protection
- ✅ Store in environment variables only
- ✅ Rotate webhooks periodically
- ✅ Monitor Discord webhooks list

### 6. CORS Configuration
- ✅ Restrict to your domain
- ✅ Don't allow `*` in production

```javascript
app.use(cors({
    origin: 'https://your-domain.com',
    methods: ['POST'],
    credentials: true
}));
```

---

## 🐛 Troubleshooting

### Issue: "Cannot reach backend"

**Check:**
- Backend server is running? `node server.js`
- Correct URL in HTML? (`BACKEND_URL`)
- Firewall allowing port 3000?
- CORS enabled?

**Fix:**
```javascript
// Enable all origins (development only)
app.use(cors());
```

### Issue: "Failed to post to Discord"

**Check:**
- Webhook URL valid? Test: `curl -X POST <URL> -H "Content-Type: application/json" -d '{"content":"test"}'`
- Webhook still exists in Discord?
- Bot has permissions?

**Fix:**
1. Recreate webhook in Discord
2. Update `.env`
3. Restart server

### Issue: "429 Too Many Requests"

**Check:**
- Rate limit settings correct?
- Test endpoint: `curl http://localhost:3000/api/health`

**Fix:**
Adjust in `.env`:
```env
RATE_LIMIT_WINDOW=60000        # 1 minute
RATE_LIMIT_MAX_REQUESTS=10     # Increase if needed
```

### Issue: Form won't submit

**Debug:**
1. Open browser console (F12 → Console)
2. Try submitting form
3. Look for error messages
4. Check Network tab for failed requests

**Common errors:**
- `CORS error` → Check CORS setup
- `404 Not Found` → Wrong backend URL
- `500 Internal Server Error` → Check server logs

---

## 📝 Testing

### Manual Test with cURL

```bash
# Test health endpoint
curl http://localhost:3000/api/health

# Test submission
curl -X POST http://localhost:3000/api/swat/log \
  -H "Content-Type: application/json" \
  -d '{
    "title": "TSO-2",
    "narrative": "Test deployment",
    "roster": "🟢 ALIVE - Officer Alpha"
  }'
```

### Load Testing

```bash
# Using Apache Bench (ab)
ab -n 100 -c 10 -p payload.json -T application/json http://localhost:3000/api/swat/log
```

---

## 📚 File Structure

```
swat-backend/
├── server.js              # Main Express app
├── .env                   # Secrets (do NOT commit)
├── .gitignore             # Ignore .env, node_modules
├── package.json           # Dependencies
├── package-lock.json      # Lock file
└── README.md              # This file
```

`.gitignore`:
```
node_modules/
.env
.env.local
*.log
```

---

## ✅ Checklist for Production

- [ ] Webhook URL in `.env` (not in code)
- [ ] HTTPS/TLS configured
- [ ] CORS restricted to your domain
- [ ] Rate limiting enabled
- [ ] Input validation & sanitization active
- [ ] Error logging setup
- [ ] Server monitoring configured
- [ ] Backup & disaster recovery plan
- [ ] Documentation updated
- [ ] Team trained on operations

---

## 📞 Support

If you encounter issues:

1. Check server logs: `node server.js` (look for errors)
2. Verify Discord webhook: Discord → Server → Webhooks
3. Test CORS: Open DevTools Console → check Network tab
4. Check rate limiting: Wait 1 minute and retry
5. Review troubleshooting section above

---

**Status**: Production Ready ✅  
**Last Updated**: 2026-05-18
