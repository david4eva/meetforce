# 🎯 Zoom-Salesforce Integration

> Create, manage, and track Zoom meetings directly from Salesforce with automated registration and attendance sync.

[![Salesforce](https://img.shields.io/badge/Salesforce-Lightning-00a1e0.svg)](https://developer.salesforce.com)
[![Zoom](https://img.shields.io/badge/Zoom-API-2d8cff.svg)](https://developers.zoom.us)
[![Heroku](https://img.shields.io/badge/Heroku-Deployed-430098.svg)](https://heroku.com)
[![License](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)

## 🚀 Features

### Core Functionality
- **One-click meeting creation** from Salesforce records
- **Automated registration management** with custom forms
- **Real-time attendance tracking** via Zoom webhooks  
- **Email notifications** for registrants and organizers
- **Bulk operations** for large-scale events
- **Native Salesforce UI** - no external tools needed

### Advanced Features  
- **Experience Cloud integration** for public registration
- **Custom registration fields** and branding
- **Attendance reporting** with exportable data
- **Recurring meeting series** support
- **Waiting room management**
- **Multi-timezone support**

## 📋 Prerequisites

### Salesforce Requirements
- Lightning Experience enabled
- Custom Object permissions
- Experience Cloud (for public registration)
- API access for integrations

### Zoom Requirements  
- Zoom Pro/Business/Enterprise account
- Developer account for OAuth app creation
- Meeting API and Webhook permissions

### Technical Requirements
- Heroku account (for middleware hosting)
- Basic understanding of Salesforce administration
- Network access to external APIs

## ⚡ Quick Start

### 1. Salesforce Package Installation

```bash
# Install from AppExchange (coming soon)
# Or deploy from source:
sfdx force:source:deploy -p force-app -u your-org-alias
sfdx force:user:permset:assign -n Zoom_Integration_User -u your-org-alias
```

### 2. Heroku Middleware Setup

```bash
# Clone and deploy middleware
git clone https://github.com/yourusername/zoom-sf-middleware
cd zoom-sf-middleware
heroku create your-app-name
git push heroku main
```

### 3. Zoom OAuth Configuration

1. Create OAuth app at [Zoom Marketplace](https://marketplace.zoom.us)
2. Configure redirect URI: `https://your-heroku-app.herokuapp.com/oauth/callback`
3. Add required scopes: `meeting:write`, `meeting:read`, `webinar:write`

### 4. Connect Your Accounts

1. Navigate to **Zoom Settings** tab in Salesforce
2. Click **Connect to Zoom**
3. Complete OAuth authorization
4. Configure default meeting settings

## 🏗️ Architecture

```
┌─────────────────┐    ┌──────────────────┐    ┌─────────────────┐
│   Salesforce    │───▶│  Heroku Python   │───▶│   Zoom API      │
│   Lightning     │    │   Middleware     │    │   Webhooks      │
│   Components    │◀───│  (Flask/FastAPI) │◀───│   OAuth 2.0     │
└─────────────────┘    └──────────────────┘    └─────────────────┘
        │                       │
        │                       ▼
        │               ┌──────────────────┐
        └──────────────▶│  PostgreSQL DB   │
                        │  (Meeting Data)  │
                        └──────────────────┘
```

### Data Flow
1. **Meeting Creation**: Salesforce → Heroku → Zoom API → Database
2. **Registration**: Public Form → Heroku → Salesforce + Zoom
3. **Attendance**: Zoom Webhooks → Heroku → Salesforce Updates

## 📊 Custom Objects

### Zoom_Meeting__c
```sql
Name                    Type            Description
─────────────────────────────────────────────────────
Meeting_ID__c          Text(50)        Zoom meeting ID
Join_URL__c            URL             Meeting join link  
Start_Time__c          DateTime        Scheduled start
Duration__c            Number          Meeting duration (minutes)
Registration_URL__c    URL             Public registration link
Status__c             Picklist        Scheduled/Live/Completed
Max_Attendees__c       Number          Registration limit
```

### Meeting_Registrant__c  
```sql
Name                    Type            Description
─────────────────────────────────────────────────────
Meeting__c             Lookup          Related Zoom_Meeting__c
Contact__c             Lookup          Related Contact (optional)
Email__c               Email           Registrant email
Registration_Time__c   DateTime        When they registered
Attendance_Status__c   Picklist        Registered/Attended/No-show
Join_Time__c           DateTime        When they joined
Leave_Time__c          DateTime        When they left
```

## 🔧 Configuration

### Environment Variables (Heroku)
```bash
ZOOM_CLIENT_ID=your_zoom_client_id
ZOOM_CLIENT_SECRET=your_zoom_client_secret  
SALESFORCE_CLIENT_ID=your_sf_connected_app_id
SALESFORCE_CLIENT_SECRET=your_sf_connected_app_secret
DATABASE_URL=postgres://... # Auto-configured by Heroku
WEBHOOK_VERIFICATION_TOKEN=your_zoom_webhook_token
```

### Salesforce Named Credential
- **URL**: `https://your-heroku-app.herokuapp.com`
- **Identity Type**: Per User
- **Authentication Protocol**: OAuth 2.0
- **Auth Provider**: Custom (Heroku OAuth)

## 📱 Usage Examples

### Create Meeting from Opportunity
```apex
// Apex trigger or flow can create meetings
Zoom_Meeting__c meeting = new Zoom_Meeting__c(
    Name = 'Product Demo - ' + opp.Name,
    Start_Time__c = DateTime.now().addDays(1),
    Duration__c = 60,
    Opportunity__c = opp.Id
);
insert meeting;
// Meeting automatically created in Zoom via process builder
```

### Public Registration Flow
```javascript
// Lightning Web Component for registration
@track registrationData = {
    firstName: '',
    lastName: '', 
    email: '',
    company: ''
};

handleRegistration() {
    // Calls Apex → Heroku → Zoom registration API
    registerForMeeting({ 
        meetingId: this.meetingId, 
        registrant: this.registrationData 
    });
}
```

## 🧪 Testing

### Salesforce Tests
```bash
# Run Apex tests
sfdx force:apex:test:run -l RunLocalTests -u your-org

# Expected coverage: 85%+ required for AppExchange
```

### Python Middleware Tests  
```bash
# Run pytest suite
cd middleware
python -m pytest tests/ -v

# Test specific functionality
pytest tests/test_zoom_api.py::test_create_meeting
```

### Integration Tests
```bash
# End-to-end testing
pytest tests/test_integration.py -k "test_full_meeting_lifecycle"
```

## 📈 Monitoring & Analytics

### Salesforce Reports
- Meeting creation success rates
- Registration conversion metrics  
- Attendance tracking by meeting type
- User adoption dashboards

### Heroku Monitoring
```bash
# View logs
heroku logs --tail -a your-app-name

# Monitor performance  
heroku addons:create newrelic:wayne -a your-app-name
```

## 🚀 Deployment

### Salesforce Package
```bash
# Create package version
sfdx force:package:version:create -p "Zoom Integration" -x

# Install in target org
sfdx force:package:install -p 04t... -u target-org
```

### Heroku Pipeline
```yaml
# .github/workflows/deploy.yml
name: Deploy to Heroku
on:
  push:
    branches: [main]
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - uses: akhileshns/heroku-deploy@v3.12.12
        with:
          heroku_api_key: ${{secrets.HEROKU_API_KEY}}
          heroku_app_name: "your-app-name"
          heroku_email: "your-email@example.com"
```

## 🤝 Contributing

1. Fork the repository
2. Create feature branch (`git checkout -b feature/amazing-feature`)
3. Commit changes (`git commit -m 'Add amazing feature'`)
4. Push to branch (`git push origin feature/amazing-feature`) 
5. Open Pull Request

### Development Setup
```bash
# Clone repository
git clone https://github.com/yourusername/zoom-salesforce-integration
cd zoom-salesforce-integration

# Setup Salesforce development
sfdx force:org:create -f config/project-scratch-def.json -a zoom-dev
sfdx force:source:push -u zoom-dev

# Setup Python environment
cd middleware
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate
pip install -r requirements.txt
```

## 📝 Roadmap

### Version 1.0 (MVP) ✅
- [x] Basic meeting creation
- [x] Registration management  
- [x] Attendance tracking
- [x] Email notifications

### Version 1.1 (Q2 2025)
- [ ] Recurring meetings
- [ ] Advanced reporting
- [ ] Custom branding
- [ ] API rate limiting improvements

### Version 2.0 (Q3 2025)  
- [ ] Webinar support
- [ ] Advanced analytics dashboard
- [ ] Mobile app integration
- [ ] Multi-language support

## ❓ FAQ

**Q: Does this work with Zoom Basic accounts?**  
A: No, requires Zoom Pro/Business/Enterprise for API access.

**Q: Can I customize registration forms?**  
A: Yes, fully customizable fields and branding via Experience Cloud.

**Q: What's the meeting limit?**  
A: Depends on your Zoom plan - we respect your account limits.

**Q: Is this AppExchange certified?**  
A: Currently in security review process (ETA: Q2 2025).

## 📞 Support

- **Documentation**: [Wiki](https://github.com/yourusername/zoom-salesforce-integration/wiki)
- **Issues**: [GitHub Issues](https://github.com/yourusername/zoom-salesforce-integration/issues)
- **Community**: [Trailblazer Community Group](https://trailblazers.salesforce.com/...)
- **Email**: support@zoomsalesforceintegration.com

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- Zoom Developer Platform for excellent API documentation
- Salesforce Trailblazer Community for integration patterns  
- Contributors and beta testers

---

**⭐ Star this repo** if you find it helpful!

**🚀 Ready to integrate?** [Get started now](#quick-start)
