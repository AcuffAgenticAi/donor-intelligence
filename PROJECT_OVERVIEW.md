# Donor Intelligence Agent - Complete Implementation

## 📋 Project Summary

The **Donor Relationship Intelligence Agent** is a comprehensive AI-powered platform for nonprofit organizations that:

1. **Predicts donor behavior** using XGBoost machine learning models
2. **Scores churn risk** for at-risk donors
3. **Generates personalized emails** using template-based automation
4. **Suggests optimal contact timing** based on donor activity patterns
5. **Integrates with CRMs** (Salesforce, HubSpot) for seamless data sync

---

## 🎯 Core Features

### 1. Donor Profiling
- **Segment Classification**: VIP, Major, Sustaining, Prospect, Lapsed
- **Engagement Metrics**: Total donations, LTV, average gift size, recency
- **Interaction Tracking**: Email opens, clicks, event attendance
- **Custom Preferences**: Contact channel, frequency preferences, subscription status

### 2. ML-Powered Predictions
- **XGBoost Model** trained on donor features
- **8 Core Features**: Days since donation, count, LTV, average amount, frequency, interactions, email engagement, tenure
- **Predictions Generated**:
  - Re-engagement probability (0-1)
  - Churn risk (Low/Medium/High/Critical)
  - Donation likelihood (0-1)
  - Expected donation amount
  - Optimal contact day & hour

### 3. Email Automation
- **4 Pre-built Templates**:
  - Reengagement (for lapsed donors)
  - Milestone (5, 10, 25, 50, 100 donations)
  - Churn Prevention (high-risk donors)
  - VIP Stewardship (major donors)
- **Dynamic Personalization**: Names, amounts, impacts, event dates
- **Intelligent Routing**: Suggest best template based on donor profile

### 4. CRM Integration
- **Salesforce Nonprofit Cloud**: Full sync, task creation, interaction logging
- **HubSpot**: Contact sync, lifecycle stage mapping, property updates
- **Bi-directional Sync**: Push predictions, pull donor data

### 5. Agent Orchestration
- **Full Cycle Analysis**: Analyze all donors in one command
- **Churn Detection**: Automatically flag at-risk segments
- **Campaign Generation**: Create personalized emails for candidates
- **CRM Synchronization**: Keep external systems up-to-date

---

## 📊 Data Models

### Donor
```python
├── Identity: first_name, last_name, email, phone
├── Profile: organization, title, segment
├── Metrics: total_donations, lifetime_value, avg_donation
├── Engagement: last_donation_date, days_since_donation
├── Preferences: contact_channel, frequency, unsubscribed
└── External: crm_id, crm_source
```

### Donation
```python
├── Amount
├── Date
├── Channel: online, check, wire, event
├── Campaign tracking
└── Notes
```

### Prediction
```python
├── Probabilities: reengagement, churn, donation_likelihood
├── Amounts: expected_donation
├── Timing: optimal_contact_day, optimal_contact_hour
├── Risk Level: low/medium/high/critical
└── Model metadata: version, features, expiration
```

### Outreach Campaign
```python
├── Email subject & body (templated + personalized)
├── Status: draft → sent → delivered → opened → clicked
├── Personalization data (JSON)
└── Tracking: sent_at, opened_at, clicked_at
```

---

## 🏗️ Architecture

```
┌─────────────────────────────────────────┐
│         FastAPI Backend                 │
├─────────────────────────────────────────┤
│                                         │
│  ┌─────────────────────────────────┐   │
│  │  Agent Orchestrator             │   │
│  │  - Coordinate workflows         │   │
│  │  - Run analysis cycles          │   │
│  └─────────────────────────────────┘   │
│           ↓ ↓ ↓                         │
│  ┌──────────────────────────────────┐  │
│  │ ML Scoring │ Email Service │ CRM │  │
│  │ - XGBoost  │ - Templates   │Sync │  │
│  │ - Predict  │ - Personalize │     │  │
│  └──────────────────────────────────┘  │
│           ↓ ↓ ↓                         │
│  ┌──────────────────────────────────┐  │
│  │    SQLModel / Async Database     │  │
│  │    (SQLite / PostgreSQL)         │  │
│  └──────────────────────────────────┘  │
│                                         │
└─────────────────────────────────────────┘
         ↓              ↓               ↓
    HubSpot      Salesforce      Next.js UI
```

---

## 🚀 API Endpoints

### Donor Management
- `POST /donors` - Create donor
- `GET /donors` - List all (with filter)
- `GET /donors/{id}` - Get details
- `PUT /donors/{id}` - Update

### Donations
- `POST /donations` - Record donation
- `GET /donations` - List with filters

### Predictions
- `POST /predictions/{donor_id}` - Generate/update
- `GET /predictions/{donor_id}` - Get latest
- `GET /churn-risks?threshold=0.6` - Get at-risk donors

### Email Campaigns
- `POST /campaigns` - Create campaign
- `GET /campaigns` - List campaigns
- `PATCH /campaigns/{id}` - Update status

### Agent Operations
- `POST /agent/analyze` - Analyze donor base
- `POST /agent/generate-campaign/{donor_id}` - Create personalized email
- `POST /agent/full-cycle` - Run entire workflow
- `POST /agent/sync-crm` - Sync to CRM

---

## 🔧 Technology Stack

**Backend**
- FastAPI 0.95+ (async REST API)
- SQLModel (async ORM)
- SQLAlchemy + asyncpg (async DB)
- XGBoost (ML predictions)
- HTTPX (async HTTP client)

**Database**
- SQLite (development)
- PostgreSQL (production)
- Supabase (managed option)

**ML/Data**
- XGBoost (tree-based classifier)
- NumPy/Pandas (data processing)
- Scikit-learn (metrics)

**Integrations**
- HubSpot API
- Salesforce API
- Email templates (Jinja2 compatible)

**Frontend** (planned)
- Next.js 14+ (React)
- TypeScript
- Tailwind CSS
- Chart.js / Recharts

---

## 📈 Typical Workflow

### 1. Initial Setup
```bash
# Create organization
POST /donors → register your nonprofit

# Load historical data
POST /donations → import past donations
```

### 2. First Analysis
```bash
# Run full cycle
POST /agent/full-cycle
↓
- Analyzes all donors
- Generates predictions
- Flags 50+ high-risk donors
- Creates campaigns for top candidates
- Syncs to HubSpot/Salesforce
```

### 3. Ongoing Operations
```bash
# Weekly: New donation recorded
POST /donations

# Monthly: Re-analyze & regenerate predictions
POST /predictions/[donor_id]
POST /agent/analyze

# As needed: Create campaigns
POST /agent/generate-campaign/[donor_id]
```

### 4. Track Results
```bash
# Monitor campaign performance
PATCH /campaigns/[id] → status: "opened"
      → automatically logs to CRM

GET /churn-risks → Updated at-risk list
GET /donors?segment=lapsed → View lapsed segment
```

---

## 💡 Use Cases

### Use Case 1: Reactivate Lapsed Donors
1. System identifies donors with 180+ days no donation
2. Scores them for re-engagement probability
3. Generates personalized "We miss you" email
4. Suggests Tuesday 10 AM as optimal send time
5. Tracks opens/clicks and logs to CRM

### Use Case 2: VIP Stewardship
1. Major donor ($10k+ LTV) identified
2. System suggests VIP stewardship template
3. Email includes exclusive event invitation
4. Logs interaction to Salesforce
5. Updates segment and lifecycle stage

### Use Case 3: Churn Prevention
1. Donor with 60%+ churn probability flagged
2. Recently showed engagement (email open)
3. System recommends preventing email
4. Suggests phone call instead
5. CRM task created for relationship manager

### Use Case 4: Predictive Fundraising
1. New donor joins with $500 first gift
2. Model predicts $250 expected next gift
3. System suggests monthly giving ask
4. Email optimized for monthly commitment language
5. Donation likely within 3 weeks

---

## 🔒 Security & Privacy

- **Data Isolation**: Each org's data in separate database context
- **API Authentication**: (future: JWT tokens)
- **CRM Credentials**: Stored as environment variables
- **GDPR Compliance**: Unsubscribe support, data retention policies
- **Email Privacy**: Campaign tracking is opt-in

---

## 📊 Performance Target

- **Prediction latency**: <100ms per donor
- **Batch analysis**: 10,000 donors in <2 minutes
- **Email generation**: 50 emails/second
- **CRM sync**: 1,000 contacts/minute
- **Uptime**: 99.9% SLA

---

## 🚀 Deployment Options

### Option 1: Local Development
```bash
# SQLite + localhost
uvicorn app.main:app --reload
```

### Option 2: Heroku/Railway
```bash
# PostgreSQL + managed dyno
pip freeze > requirements.txt
git push heroku main
```

### Option 3: Docker + Cloud Run
```bash
docker build -t donor-intelligence .
gcloud run deploy donor-intelligence --image donor-intelligence
```

### Option 4: AWS / Azure / GCP
- RDS PostgreSQL for database
- Lambda / Cloud Functions for serverless
- SageMaker / Vertex AI for ML model hosting

---

## 📚 Documentation

- **API Docs**: http://localhost:8000/docs (Swagger UI)
- **README**: `backend/README.md` (detailed setup)
- **Models**: `app/models.py` (database schema)
- **Agent**: `app/agent.py` (orchestration logic)
- **ML**: `app/ml_scoring.py` (prediction code)

---

## 🎓 Next Steps

1. ✅ **Core Backend** - FastAPI, models, database
2. ✅ **ML Predictions** - XGBoost scoring model
3. ✅ **Email Automation** - Template-based generation
4. ✅ **CRM Integration** - HubSpot & Salesforce sync
5. ✅ **Agent Orchestrator** - Full cycle automation
6. 🔲 **Frontend Dashboard** - Next.js UI
7. 🔲 **Real Email Sending** - SendGrid / Mailgun integration
8. 🔲 **Advanced ML** - RFM segmentation, CLV prediction
9. 🔲 **A/B Testing** - Email subject line optimization
10. 🔲 **Webhooks** - Real-time event processing

---

## 📞 Support & Contributions

Built with ❤️ for nonprofit organizations.

For questions or contributions, open an issue or pull request.

---

**Version**: 1.0.0  
**Last Updated**: March 3, 2025  
**Status**: Production Ready (Core Features)
