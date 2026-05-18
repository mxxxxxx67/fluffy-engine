# Fluffy Engine 🚁

A collection of operational tools and utilities for deployment logging and automation.

## 📋 Contents

### SWAT Deployment Log
An interactive form-based logging system for tracking tactical operations with Discord integration.

**Quick Start:**
1. Your admin sets up the backend server (see [`BACKEND_SETUP.md`](./BACKEND_SETUP.md))
2. Users access the form at the hosted URL
3. Fill in operation details, officer roster, and narrative
4. Submit to automatically post to Discord
5. **No webhook URL needed** - all users submit securely through your server

**Learn more:** See [`BACKEND_SETUP.md`](./BACKEND_SETUP.md)

## 🚀 Features

- **Multi-User Access**: Multiple officers can submit logs simultaneously
- **Secure Submission**: Webhook URL never exposed to clients
- **Dynamic Officer Management**: Add/remove personnel in real-time
- **Operation Tracking**: Document deployment details with timestamps
- **Discord Integration**: Automatic log posting via webhooks
- **Status Indicators**: Track officer status (Alive/Knocked)
- **Rich Formatting**: Professional Discord embed output
- **Template System**: Pre-built narrative templates for common scenarios

## 📁 Project Structure

```
fluffy-engine/
├── swat-deployment-log.html        # Multi-user form (no webhook exposed)
├── BACKEND_SETUP.md                # Backend implementation guide
├── SWAT_DEPLOYMENT_SETUP.md        # Original setup guide (archived)
└── README.md                        # This file
```

## 🔧 Technologies Used

- **HTML5**: Semantic markup and structure
- **CSS3**: Modern styling with Discord-inspired theme
- **JavaScript**: Dynamic form handling and backend API calls
- **Discord Webhooks**: Real-time log delivery via backend
- **Backend**: Node.js/Express, Python/Flask, or serverless (Vercel/Heroku)

## 📖 Documentation

| Document | Purpose |
|----------|---------|
| [`BACKEND_SETUP.md`](./BACKEND_SETUP.md) | Complete backend implementation guide with code examples |
| [`SWAT_DEPLOYMENT_SETUP.md`](./SWAT_DEPLOYMENT_SETUP.md) | Original single-user setup (reference) |
| This README | Project overview and quick reference |

## ⚙️ Configuration

### For Admins (Backend Setup)

1. **Create a Discord webhook** in your server
2. **Choose a backend** (Node.js, Python, or serverless)
3. **Store webhook URL** in environment variables
4. **Deploy backend** to your server
5. **Update form endpoint**: Edit `BACKEND_URL` in HTML to point to your backend
6. **Share form URL** with team

See [`BACKEND_SETUP.md`](./BACKEND_SETUP.md) for step-by-step instructions.

### For Users (Frontend)

- Navigate to the hosted form URL
- Select operation type (TSO-1 through TSO-XO)
- Add officers and their status
- Use template buttons to build narrative
- Submit log to Discord

### Operation Designations
- **TSO-1**: Standard tactical operation level 1
- **TSO-2**: Standard tactical operation level 2
- **TSO-3**: Standard tactical operation level 3
- **TSO-X**: Extended tactical operation
- **TSO-XO**: Extended tactical operation - optimized

## 🔐 Security

- ✅ **Webhook URL protected**: Never exposed to client browsers
- ✅ **Backend validation**: All input validated server-side
- ✅ **Rate limiting**: Prevent submission spam (configurable)
- ✅ **Secure transmission**: HTTPS recommended in production
- ✅ **Audit trail**: All submissions logged on backend

## 💡 Usage Example

### Frontend (User)
```
Operation Title: TSO-2
Officers:
  🟢 ALIVE - Officer Alpha (Badge #001)
  🟢 ALIVE - Officer Bravo (Badge #002)
  🔴 KNOCKED - Officer Charlie (Badge #003)

Narrative:
  Building clearance completed successfully. 
  Officers Alpha and Bravo secured the perimeter.
  Officer Charlie sustained non-critical injuries during breach.
  All objectives accomplished.
```

### Discord Output
```
🚨 DEPLOYMENT LOG: TSO-2

👥 Personnel Roster & Status
🟢 ALIVE - Officer Alpha (Badge #001)
🟢 ALIVE - Officer Bravo (Badge #002)
🔴 KNOCKED - Officer Charlie (Badge #003)

📝 Operation Narrative
Building clearance completed successfully...
```

## 🎯 Template System

Four pre-built templates to accelerate narrative creation:

1. **Ammu-Nation Style**: Complex multi-location engagement
2. **Barricaded Suspects**: Negotiation failed, tactical entry
3. **Pursuit + Air Support**: Chase with helicopter/drone
4. **Major Incident**: Large-scale coordinated response

Click a template to load it (replaces current text).

## 🐛 Troubleshooting

### Users

| Issue | Solution |
|-------|----------|
| Form won't load | Check backend URL is correct and server is running |
| Submission fails | All fields required? Backend responding? |
| Log not in Discord | Check backend logs for errors |

### Admins

See [`BACKEND_SETUP.md`](./BACKEND_SETUP.md) **Troubleshooting** section for:
- Webhook errors
- CORS issues
- Rate limiting problems
- Deployment issues

## 🚀 Deployment Options

### Quick Start (Development)
```bash
# Node.js / Express
git clone <repo>
export DISCORD_WEBHOOK_URL="your_webhook"
node server.js
# Form accessible at http://localhost:3000
```

### Production Platforms
- **Heroku**: `git push heroku main`
- **Vercel**: `vercel deploy`
- **AWS Lambda**: Serverless with API Gateway
- **DigitalOcean**: VPS with Docker/PM2

See [`BACKEND_SETUP.md`](./BACKEND_SETUP.md) for detailed deployment guides.

## 📝 Contributing

To contribute improvements:
1. Test changes locally with backend
2. Document modifications
3. Submit pull requests with descriptions
4. Update documentation as needed

## 📄 License

This project is part of the fluffy-engine repository.

## 👤 Owner

**Repository**: mxxxxxx67/fluffy-engine  
**Created**: 2026-05-18  
**Updated**: 2026-05-18 (Multi-user backend architecture)

---

**Status**: Active & Production Ready ✅  
**Architecture**: Secure Backend Proxy Model 🔐  
**Users**: Multiple (concurrent supported)
