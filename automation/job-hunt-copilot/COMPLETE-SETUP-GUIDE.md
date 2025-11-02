# Job Hunt Co-Pilot - Complete Setup Guide

## 🎯 What This Workflow Does

Automates your entire job search process:

1. **Searches multiple job sites** hourly via Apify
2. **Filters fresh jobs** (last 24 hours) matching your keywords
3. **Loads your resume** from Google Drive
4. **AI scores job-resume fit** (0-100) using OpenAI
5. **Logs all jobs** to Google Sheets with scores
6. **For high matches (≥70%)**:
   - Searches LinkedIn for recruiters
   - Drafts personalized outreach messages
   - Logs to separate "High Matches" sheet
   - Sends email notifications

## ⚠️ Important Notes

**This is an ADVANCED workflow** requiring:
- Multiple paid API subscriptions
- Technical setup (APIs, OAuth, webhooks)
- ~$50-100/month in API costs (depending on volume)
- Time investment (~2-3 hours setup)

**LinkedIn Scraping Warning**: LinkedIn actively blocks automated scraping. The recruiter search may not work reliably without:
- Residential proxies
- LinkedIn Premium account
- or a specialized LinkedIn API service

## 📋 Required Services & Costs

### 1. **Apify** (Job Scraping)
- **Purpose**: Scrapes jobs from multiple sites (LinkedIn, Indeed, etc.)
- **Cost**: $49/month starter plan (includes 100 compute units)
- **Sign up**: https://apify.com/pricing
- **Free trial**: 7 days

### 2. **OpenAI API** (AI Scoring & Outreach)
- **Purpose**: Scores resume fit, generates outreach messages
- **Cost**: Pay-per-use (~$10-30/month depending on volume)
- **Sign up**: https://platform.openai.com/signup
- **Models needed**: GPT-4o-mini

### 3. **Google Workspace** (Sheets & Drive)
- **Purpose**: Resume storage, job logging, config
- **Cost**: Free (personal account)
- **Requires**: OAuth setup

### 4. **Email SMTP** (Notifications)
- **Purpose**: High-match job alerts
- **Cost**: Free (Gmail SMTP) or paid (SendGrid, Mailgun)
- **Options**:
  - Gmail: Free, 500 emails/day limit
  - SendGrid: Free tier (100 emails/day)

### 5. **n8n** (Already installed!)
- **Purpose**: Workflow automation platform
- **Cost**: Free (self-hosted)

## 🛠️ Prerequisites

Before starting, you need:

- ✅ n8n running (you have this!)
- ✅ OpenAI API key (you have this!)
- ✅ Google account
- ❌ Apify account (need to create)
- ❌ Resume in Google Drive
- ❌ Email SMTP configured

## 📊 Step 1: Set Up Google Sheets

### Create New Spreadsheet

1. Go to: https://sheets.google.com
2. Create new spreadsheet named: "Job Hunt Tracker"
3. Note the Sheet ID from URL:
   ```
   https://docs.google.com/spreadsheets/d/SHEET_ID_HERE/edit
   ```

### Sheet 1: "Career Sites" (Search Config)

| search_term         | location | country       |
|---------------------|----------|---------------|
| software engineer   | Remote   | United States |
| AI engineer         | New York | United States |
| backend developer   | Remote   | United States |

**Columns**:
- `search_term`: Job title/keyword to search
- `location`: Geographic location or "Remote"
- `country`: Country name

### Sheet 2: "Scored Jobs" (All Jobs Log)

| title | company | link | score | keywords | gaps | platform | timestamp |
|-------|---------|------|-------|----------|------|----------|-----------|

**Columns**:
- `title`: Job title
- `company`: Company name
- `link`: Job posting URL
- `score`: AI fit score (0-100)
- `keywords`: Matched skills
- `gaps`: Missing requirements
- `platform`: Source (LinkedIn, Indeed, etc.)
- `timestamp`: When processed

### Sheet 3: "High Matches Outreach" (Priority Jobs)

| title | company | link | score | recruiters | outreach_message | timestamp |
|-------|---------|------|-------|------------|------------------|-----------|

**Columns**:
- `title`: Job title
- `company`: Company name
- `link`: Job posting URL
- `score`: AI fit score (70-100)
- `recruiters`: Found recruiter names
- `outreach_message`: AI-generated message
- `timestamp`: When processed

## 📄 Step 2: Upload Resume to Google Drive

### Prepare Your Resume

1. **Format**: Plain text (.txt) or PDF
2. **Content**: Include:
   - Skills
   - Experience
   - Education
   - Projects
   - Certifications

### Upload to Drive

1. Go to: https://drive.google.com
2. Create folder: "Job Hunt"
3. Upload your resume
4. Right-click → **Get link** → **Anyone with link can view**
5. Note the File ID from URL:
   ```
   https://drive.google.com/file/d/FILE_ID_HERE/view
   ```

**Important**: If resume is PDF, you'll need to:
- Convert to text first, OR
- Add PDF parsing (see Advanced Setup)

## 🔑 Step 3: Set Up Apify

### Create Account

1. Go to: https://apify.com/sign-up
2. Sign up (free 7-day trial)
3. After trial, choose plan (Starter $49/month recommended)

### Get API Token

1. Go to: https://console.apify.com/account/integrations
2. Copy **API Token**
3. Save it securely

### Find Job Scraper Actor

The workflow uses actor: `jpraRc4MCUh5ehbHV`

This is likely a LinkedIn/Indeed job scraper. You may need to:
1. Browse Apify Store: https://apify.com/store
2. Search for "job scraper" or "LinkedIn jobs"
3. Select one that matches your needs
4. Note the Actor ID (format: `username/actor-name`)

**Popular job scrapers**:
- LinkedIn Jobs Scraper
- Indeed Job Scraper
- Multi-platform Job Aggregator

## 🔧 Step 4: Configure n8n Credentials

### Google Sheets OAuth

1. In n8n: **Credentials** → **Add** → "Google Sheets OAuth2 API"
2. Click **Connect**
3. Sign in with Google
4. Grant permissions
5. Name: "Google Sheets - Job Hunt"

### Google Drive OAuth

1. **Credentials** → **Add** → "Google Drive OAuth2 API"
2. Click **Connect**
3. Sign in with Google
4. Grant permissions
5. Name: "Google Drive - Job Hunt"

### OpenAI API

1. **Credentials** → **Add** → "OpenAI"
2. Enter your API key
3. Name: "OpenAI - Job Hunt"

### Email SMTP

**Option A: Gmail**

1. **Credentials** → **Add** → "SMTP"
2. Configuration:
   ```
   Host: smtp.gmail.com
   Port: 587
   Security: STARTTLS
   User: your@gmail.com
   Password: [App Password]
   ```
3. **Get App Password**:
   - Google Account → Security → 2-Step Verification → App passwords
   - Generate new app password
   - Copy and use in n8n

**Option B: SendGrid**

1. Sign up: https://sendgrid.com
2. Create API key
3. In n8n: **Credentials** → **Add** → "SMTP"
4. Configuration:
   ```
   Host: smtp.sendgrid.net
   Port: 587
   User: apikey
   Password: [Your SendGrid API Key]
   ```

## 📥 Step 5: Import & Configure Workflow

### Import Workflow

1. In n8n: **Workflows** → **Import from File**
2. Select: `workflow-original.json`
3. Name it: "Job Hunt Co-Pilot"

### Update Configuration

Open the workflow and update these nodes:

#### 1. **Read Search Configs** Node
```
Document ID: YOUR_SHEETS_ID
Sheet Name: Career Sites
Range: A:C
```

#### 2. **Fetch Jobs via Apify** Node
```
URL: https://api.apify.com/v2/acts/YOUR_ACTOR_ID/run-sync-get-dataset-items?token=YOUR_APIFY_TOKEN
```

Replace:
- `YOUR_ACTOR_ID`: Your Apify actor (e.g., `jpraRc4MCUh5ehbHV`)
- `YOUR_APIFY_TOKEN`: Your Apify API token

#### 3. **Filter Fresh & Keywords** Node

Edit the JavaScript code to customize:
```javascript
const keyword = 'software engineer'; // Your target role
```

Change to your preferred job title/keywords.

#### 4. **Load Resume** Node
```
File ID: YOUR_RESUME_FILE_ID
```

Paste your Google Drive file ID.

#### 5. **Log Scored Jobs to Sheet** Node
```
Document ID: YOUR_SHEETS_ID
Sheet Name: Scored Jobs
```

#### 6. **Set User Profile** Node

Update your profile:
```json
{
  "name": "Your Full Name",
  "likes": "AI, automation, hiking, photography",
  "skills": "Python, JavaScript, React, n8n, OpenAI"
}
```

#### 7. **Log High Matches to Sheet** Node
```
Document ID: YOUR_SHEETS_ID
Sheet Name: High Matches Outreach
```

#### 8. **Email Notification** Node
```
From Email: your@email.com
To Email: your@email.com
```

### Assign Credentials

For each node, select the matching credential:
- Google Sheets nodes → "Google Sheets - Job Hunt"
- Google Drive node → "Google Drive - Job Hunt"
- OpenAI nodes → "OpenAI - Job Hunt"
- Email node → Your SMTP credential

## 🧪 Step 6: Test the Workflow

### Change Trigger to Manual

1. Delete "Cron Trigger" node (top-left)
2. Add "Manual Trigger" node
3. Connect to "Read Search Configs"

### Test Each Section

**Test 1: Google Sheets Read**
1. Execute "Read Search Configs" node only
2. Verify it reads your search terms
3. Should show your search terms, location, country

**Test 2: Apify Job Fetch** (May fail without paid Apify)
1. Execute up to "Fetch Jobs via Apify"
2. Check response for job data
3. If error, verify Apify token and actor ID

**Test 3: Resume Loading**
1. Execute "Load Resume" node
2. Verify file downloads
3. Check "Extract Resume Text" output

**Test 4: AI Scoring**
1. Create mock job data
2. Execute "OpenAI Score Fit"
3. Verify score extraction

**Test 5: Google Sheets Write**
1. Execute "Log Scored Jobs to Sheet"
2. Check your Sheets for new row

**Test 6: High Match Flow**
1. Create mock high-score job (score=85)
2. Execute IF node true path
3. Verify outreach generation

**Test 7: Email**
1. Execute "Email Notification"
2. Check your inbox
3. Verify email received

## 🚀 Step 7: Activate Automation

### Re-enable Cron Trigger

1. Delete Manual Trigger
2. Add back "Cron Trigger" node
3. Set schedule:
   ```
   Every 1 hour (default)
   or
   Every 2 hours (less aggressive)
   or
   Twice per day (morning & evening)
   ```

### Activate Workflow

1. Toggle **Active** switch in top-right
2. Workflow will run on schedule
3. Monitor executions in n8n dashboard

## 💰 Cost Breakdown

### Per Execution (assuming 50 jobs found):

- **Apify**: ~$0.50 (compute units)
- **OpenAI Scoring**: 50 × $0.001 = $0.05
- **OpenAI Outreach** (5 high matches): 5 × $0.002 = $0.01
- **Google/Email**: Free
- **Total**: ~$0.56 per execution

### Monthly Cost (Running hourly):

- **Executions**: 24/day × 30 days = 720/month
- **Apify**: $49/month (fixed)
- **OpenAI**: ~$43/month (variable)
- **Email**: Free
- **Total**: ~$92/month

### Cost Optimization:

1. **Run less frequently**: Every 2-3 hours instead of hourly
2. **Reduce Apify results**: Set `max_results: 25` instead of 50
3. **Filter before AI scoring**: Only score if keywords match
4. **Use cheaper Apify actors**: Some cost less compute units

## 🐛 Troubleshooting

### Apify Returns No Jobs

**Causes**:
- Actor ID incorrect
- API token invalid
- Search terms too specific
- No jobs posted in last 24 hours

**Fix**:
- Verify actor ID in Apify console
- Test Apify directly in their UI
- Broaden search terms
- Extend time window to 3 days

### Resume Not Loading

**Causes**:
- File ID incorrect
- File not shared publicly
- PDF format (needs parsing)

**Fix**:
- Verify file ID from Drive URL
- Set file sharing to "Anyone with link"
- Convert PDF to .txt first

### OpenAI Scoring Fails

**Causes**:
- API key invalid
- Resume text too long (>8000 tokens)
- Job description empty

**Fix**:
- Verify API key has credits
- Truncate resume to essentials
- Add validation before scoring

### LinkedIn Recruiter Search Fails

**Causes**:
- LinkedIn blocks automated requests
- No recruiters found
- HTML structure changed

**Fix**:
- This is expected! LinkedIn actively blocks bots
- Consider using:
  - LinkedIn Premium API (expensive)
  - Third-party recruiter databases
  - Manual recruiter research
- Or skip this step (remove nodes 13-14)

### No High Matches

**Causes**:
- All jobs scored <70
- Resume-job mismatch
- AI scoring too strict

**Fix**:
- Lower threshold in IF node (try 60)
- Improve resume keywords
- Broaden job search terms

### Email Not Sending

**Causes**:
- SMTP credentials incorrect
- Gmail app password needed
- Rate limits exceeded

**Fix**:
- Test SMTP credentials separately
- Use Gmail app password (not regular password)
- Switch to SendGrid if Gmail blocks

## 🔒 Security & Privacy

### API Keys

- Store API keys securely in n8n credentials
- Never share or commit API keys
- Rotate keys regularly

### Resume Data

- Your resume is loaded into n8n memory
- OpenAI processes it (check their privacy policy)
- Data not stored permanently
- Consider using redacted resume for testing

### Email Privacy

- Emails contain your job matches
- Use secure SMTP (TLS/SSL)
- Don't forward to public addresses

### LinkedIn Compliance

- Automated LinkedIn scraping violates their ToS
- Account may be restricted/banned
- Use at your own risk
- Consider official LinkedIn API or services

## 📈 Advanced Enhancements

### 1. Add PDF Resume Parsing

Install PDF parser:
```bash
docker exec n8n npm install pdf-parse
```

Update "Extract Resume Text" node to handle PDFs.

### 2. Add Deduplication

- Store posted job URLs in sheet
- Check before processing
- Skip if already seen

### 3. Add Multi-Resume Support

- Store multiple resume versions
- Match resume to job type
- Use best-fit resume for scoring

### 4. Add Application Tracking

- Add "Applied" sheet
- Move jobs after applying
- Track application status
- Follow-up reminders

### 5. Use Real LinkedIn API

Instead of scraping:
- LinkedIn Talent Solutions API
- RapidAPI LinkedIn endpoints
- Third-party recruiter databases

### 6. Add Slack/Discord Notifications

Replace email with:
- Slack webhook for team channels
- Discord webhook for communities
- Telegram bot for mobile alerts

### 7. Add Auto-Apply

**WARNING**: Use carefully!
- Parse application forms
- Auto-fill from resume
- Submit applications
- Requires form-specific logic

## 📚 Resources

### Documentation

- **Apify Docs**: https://docs.apify.com
- **OpenAI API**: https://platform.openai.com/docs
- **n8n Docs**: https://docs.n8n.io
- **Google Sheets API**: https://developers.google.com/sheets

### Apify Actors

- **Job Scrapers**: https://apify.com/store?category=JOBS
- **LinkedIn**: https://apify.com/store?search=linkedin%20jobs
- **Indeed**: https://apify.com/store?search=indeed%20jobs

### Alternative Services

Instead of Apify:
- **SerpAPI**: Job search API
- **Adzuna API**: Job aggregator
- **Indeed API**: Official API (apply for access)
- **LinkedIn API**: Official but expensive

## ✅ Success Checklist

Before going live:

- [ ] Google Sheets created with 3 tabs
- [ ] Resume uploaded to Google Drive (public link)
- [ ] Apify account created & API token obtained
- [ ] Apify actor selected & tested
- [ ] OpenAI API key configured
- [ ] Email SMTP configured & tested
- [ ] All n8n credentials added
- [ ] Workflow imported
- [ ] All nodes configured with IDs
- [ ] Test execution completed successfully
- [ ] At least one job logged to "Scored Jobs" sheet
- [ ] Email notification received
- [ ] Cron trigger configured
- [ ] Workflow activated

## 🆘 Getting Help

If you get stuck:

1. Check n8n execution logs (click failed node)
2. Test each API separately (Apify UI, OpenAI Playground)
3. Verify all credentials are valid
4. Check API rate limits and quotas
5. Review this guide's Troubleshooting section

## 🎉 Next Steps

Once working:

1. **Monitor for 1 week**: Check results quality
2. **Tune parameters**: Adjust score threshold, keywords
3. **Optimize costs**: Reduce frequency if needed
4. **Add enhancements**: Pick from Advanced section
5. **Track success**: Apply to high matches, track interviews

Good luck with your job hunt! 🚀
