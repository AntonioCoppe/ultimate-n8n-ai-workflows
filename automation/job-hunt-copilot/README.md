# Job Hunt Co-Pilot

Automated job search assistant that finds, scores, and helps you apply to relevant positions.

## 🎯 What It Does

- **Automatically searches** multiple job sites hourly
- **AI scores** each job against your resume (0-100)
- **Finds recruiters** on LinkedIn for high-match positions
- **Generates personalized** outreach messages
- **Logs everything** to Google Sheets
- **Sends email alerts** for top matches

## ⚠️ Important Warning

**This is an ADVANCED workflow** requiring:
- **Multiple paid services** (Apify $49/mo, OpenAI ~$30/mo)
- **2-3 hours setup time**
- **Technical knowledge** (APIs, OAuth, debugging)
- **~$90/month** in API costs

**LinkedIn Scraping**: May not work reliably (LinkedIn blocks automation). Consider skipping recruiter search or using official API.

## 📋 Requirements

| Service | Purpose | Cost | Required |
|---------|---------|------|----------|
| **Apify** | Job scraping | $49/mo | ✅ Yes |
| **OpenAI** | AI scoring | ~$30/mo | ✅ Yes |
| **Google Workspace** | Sheets & Drive | Free | ✅ Yes |
| **Email SMTP** | Notifications | Free | ✅ Yes |
| **n8n** | Automation | Free | ✅ Yes |

## 📚 Documentation

**Read the complete setup guide**: [`COMPLETE-SETUP-GUIDE.md`](./COMPLETE-SETUP-GUIDE.md)

This guide includes:
- Step-by-step setup instructions
- Google Sheets templates
- API configuration
- Credential setup
- Testing procedures
- Troubleshooting
- Cost optimization
- Security considerations

## 🚀 Quick Start

1. **Read the complete guide** (seriously, it's detailed for a reason)
2. **Create accounts**: Apify, OpenAI (if you don't have)
3. **Set up Google Sheets** (3 sheets: Config, Jobs, High Matches)
4. **Upload resume** to Google Drive
5. **Configure n8n credentials** (5 services)
6. **Import workflow** and update IDs
7. **Test manually** before activating
8. **Enable cron trigger** to run hourly

## 💰 Cost Estimate

**Monthly cost**: ~$90
- Apify: $49/mo (fixed)
- OpenAI: ~$30-40/mo (variable)
- Email: Free
- Google: Free

**Per execution**: ~$0.56
- 50 jobs scraped
- 50 AI scores
- 5 outreach messages

**Running hourly**: 720 executions/month

**Cost optimization**: Run every 2-3 hours instead of hourly to save 50-66%.

## 🎓 Skill Level Required

- **Beginner**: Not recommended
- **Intermediate**: Challenging but doable with guide
- **Advanced**: Straightforward

**Knowledge needed**:
- APIs and webhooks
- OAuth authentication
- JSON/JavaScript basics
- Debugging workflows
- Google Sheets formulas (basic)

## ⚖️ Pros & Cons

### Pros ✅
- Fully automated job search
- AI-powered resume matching
- Personalized outreach at scale
- Centralized tracking in Sheets
- Email alerts for top matches
- Saves hours of manual searching

### Cons ❌
- Expensive ($90/month)
- Complex setup (2-3 hours)
- LinkedIn scraping unreliable
- Requires multiple paid services
- Needs monitoring and tuning
- May miss some job sites

## 🔧 Troubleshooting

**Common issues**:
1. **Apify returns no jobs**: Check actor ID, search terms
2. **Resume won't load**: Verify Drive file ID, make public
3. **OpenAI errors**: Check API credits, resume length
4. **LinkedIn fails**: Expected! LinkedIn blocks bots
5. **No high matches**: Lower score threshold from 70 to 60

See full guide for detailed troubleshooting.

## 📊 Google Sheets Structure

### Sheet 1: Career Sites
```
search_term | location | country
software engineer | Remote | United States
```

### Sheet 2: Scored Jobs
```
title | company | link | score | keywords | gaps | platform | timestamp
```

### Sheet 3: High Matches Outreach
```
title | company | link | score | recruiters | outreach_message | timestamp
```

## 🔒 Security Notes

- **API Keys**: Store in n8n credentials only
- **Resume**: Processed by OpenAI (review their privacy policy)
- **LinkedIn**: Violates ToS, use at own risk
- **Email**: Use secure SMTP with TLS/SSL

## 🚧 Current Limitations

1. **LinkedIn scraping**: Unreliable, may get blocked
2. **Single resume**: Doesn't adapt resume per job
3. **No auto-apply**: Requires manual application
4. **Limited sites**: Only what Apify actor supports
5. **Static scoring**: Same criteria for all jobs

## 🎯 Ideal For

- ✅ Active job seekers with budget
- ✅ People applying to many positions
- ✅ Those who value time over money
- ✅ Tech-savvy users comfortable with APIs
- ✅ Remote/distributed job hunters

## 🚫 Not Ideal For

- ❌ Casual job seekers
- ❌ Those on tight budget
- ❌ Non-technical users
- ❌ One-off job applications
- ❌ Highly specialized niche roles

## 🔄 Alternatives

Free/cheaper options:
- **Google Alerts**: Free job alerts
- **LinkedIn Job Alerts**: Free, but limited
- **Indeed/Glassdoor**: Built-in alerts
- **Job board RSS feeds**: Free with RSS reader
- **Browser extensions**: JobSeer, Huntr (free tiers)

## 📈 Success Metrics

Track these to measure ROI:
- Jobs found per week
- High matches (score ≥70)
- Applications submitted
- Interviews scheduled
- Time saved vs. manual search
- Cost per interview
- Cost per offer

## 🎉 Expected Results

**After 1 month**:
- 300-500 jobs logged
- 50-100 high matches
- 20-30 personalized outreach messages
- 5-10 recruiter connections
- 2-5 interviews (if you apply promptly)

**Success rate depends on**:
- Resume quality
- Job market conditions
- Application speed
- Follow-up diligence

## 📝 License

Part of Ultimate n8n AI Workflows repository.
MIT License.

## 🆘 Support

- Read: [`COMPLETE-SETUP-GUIDE.md`](./COMPLETE-SETUP-GUIDE.md)
- Check: n8n execution logs
- Test: Each service API separately
- Debug: Use manual trigger mode

## 🙏 Acknowledgments

Created for professionals serious about optimizing their job search through automation.

**Remember**: This workflow finds opportunities, but YOU still need to:
- Apply promptly
- Customize applications
- Follow up with recruiters
- Prepare for interviews
- Negotiate offers

Good luck! 🚀
