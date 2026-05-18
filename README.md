# Fluffy Engine 🚁

A collection of operational tools and utilities for deployment logging and automation.

## 📋 Contents

### SWAT Deployment Log
An interactive form-based logging system for tracking tactical operations with Discord integration.

**Quick Start:**
1. Open `swat-deployment-log.html` in your browser
2. Configure your Discord webhook URL (first use only)
3. Fill in operation details, officer roster, and narrative
4. Submit to automatically post to Discord

**Learn more:** See [`SWAT_DEPLOYMENT_SETUP.md`](./SWAT_DEPLOYMENT_SETUP.md)

## 🚀 Features

- **Dynamic Officer Management**: Add/remove personnel in real-time
- **Operation Tracking**: Document deployment details with timestamps
- **Discord Integration**: Automatic log posting via webhooks
- **Status Indicators**: Track officer status (Alive/Knocked)
- **Rich Formatting**: Professional Discord embed output

## 📁 Project Structure

```
fluffy-engine/
├── swat-deployment-log.html        # Main deployment logging form
├── SWAT_DEPLOYMENT_SETUP.md        # Setup and configuration guide
└── README.md                        # This file
```

## 🔧 Technologies Used

- **HTML5**: Semantic markup and structure
- **CSS3**: Modern styling with Discord-inspired theme
- **JavaScript**: Dynamic form handling and Discord API integration
- **Discord Webhooks**: Real-time log delivery

## 📖 Documentation

| Document | Purpose |
|----------|---------|
| [`SWAT_DEPLOYMENT_SETUP.md`](./SWAT_DEPLOYMENT_SETUP.md) | Detailed setup instructions and usage guide |
| This README | Project overview and quick reference |

## ⚙️ Configuration

### Discord Webhook Setup
1. Create a webhook in your Discord server
2. Copy the webhook URL
3. Enter it in the form on first use
4. The URL is stored locally in your browser

### Operation Designations
- **TSO-1**: Standard tactical operation level 1
- **TSO-2**: Standard tactical operation level 2
- **TSO-3**: Standard tactical operation level 3
- **TSO-X**: Extended tactical operation
- **TSO-XO**: Extended tactical operation - optimized

## 🔐 Security

- ⚠️ Webhook URLs are stored in browser localStorage
- ✅ Use in controlled, secure environments
- ✅ Restrict Discord channel access to authorized users
- ✅ Rotate webhooks periodically

## 💡 Usage Example

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

## 🐛 Troubleshooting

**Issue**: Form won't submit
- ✓ All required fields filled?
- ✓ At least one officer entered?
- ✓ Discord webhook URL configured?

**Issue**: Discord not receiving logs
- ✓ Webhook URL is valid?
- ✓ Discord channel is accessible?
- ✓ Webhook hasn't been deleted?

See [`SWAT_DEPLOYMENT_SETUP.md`](./SWAT_DEPLOYMENT_SETUP.md) for detailed troubleshooting.

## 📝 Contributing

To contribute improvements:
1. Test changes locally
2. Document modifications
3. Submit pull requests with descriptions
4. Update documentation as needed

## 📄 License

This project is part of the fluffy-engine repository.

## 👤 Owner

**Repository**: mxxxxxx67/fluffy-engine  
**Created**: 2026-05-18

---

**Status**: Active ✅  
**Last Updated**: 2026-05-18
