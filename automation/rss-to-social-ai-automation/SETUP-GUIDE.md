# Complete Setup Guide

## ✅ What's Installed

I've installed these community nodes in your n8n:
1. **n8n-nodes-linked-api** - LinkedIn posting and automation
2. **n8n-nodes-hdw** - Twitter/X, LinkedIn, Reddit, Instagram support

n8n has been restarted and is ready at: http://localhost:5678

## 📊 Your Google Sheet

**URL**: https://docs.google.com/spreadsheets/d/1j4_BlQAkrNhkijEBLL48nGJ6aEVzeyRy23_zuCFiqAc/edit

### Required Columns (Sheet1)

Make sure your Google Sheet has these column headers in row 1:

| Timestamp | Title | Link | Score | Feed | Tweet | LinkedIn | Status | Posted |
|-----------|-------|------|-------|------|-------|----------|--------|--------|

The workflow will automatically populate these columns.

## 🚀 Setup Steps

### Step 1: Import Workflow

1. Open n8n: http://localhost:5678
2. Go to **Workflows** → **Import from File**
3. Import: `workflow-complete-with-sheets.json`

### Step 2: Set Up Google Sheets Credentials

1. In n8n, go to **Credentials** → **Add Credential**
2. Search for "Google Sheets"
3. Select **Google Sheets OAuth2 API**
4. Click **Connect my account**
5. Sign in with your Google account
6. Grant permissions to access Google Sheets
7. Name it: **"Google Sheets Account"**

### Step 3: Set Up OpenAI Credentials

1. Go to **Credentials** → **Add Credential**
2. Search for "OpenAI"
3. Add your API key (the one you just created: `rss-to-linkedin`)
4. Name it: **"OpenAI Account"**

### Step 4: Test the Workflow

1. Open the imported workflow
2. Click **"Execute Workflow"**
3. Wait ~20 seconds
4. Check your Google Sheet - you should see new rows!

## 📋 What the Workflow Does

### For High-Score Items (Score ≥7):
1. ✅ Scores article with AI (0-10)
2. ✅ Generates Tweet text (280 chars)
3. ✅ Generates LinkedIn post
4. ✅ Logs to Google Sheets with status "Ready to Post"
5. ✅ Sets Posted = FALSE (you'll manually post for now)

### For Low-Score Items (Score <7):
1. ✅ Scores article with AI
2. ✅ Logs to Google Sheets with status "Filtered (Score < 7)"
3. ✅ No text generation (saves API costs)

## 📊 Google Sheet Structure

### Columns Explained:

- **Timestamp**: When the article was processed
- **Title**: Article headline
- **Link**: URL to the article
- **Score**: AI relevance score (0-10)
- **Feed**: Source of the article (e.g., "Hacker News")
- **Tweet**: Generated tweet text (empty if filtered)
- **LinkedIn**: Generated LinkedIn post (empty if filtered)
- **Status**: "Ready to Post" or "Filtered (Score < 7)"
- **Posted**: FALSE by default (manually set to TRUE after posting)

### How to Use the Sheet:

1. **Review Generated Posts**:
   - Look at rows with Status = "Ready to Post"
   - Review the Tweet and LinkedIn text
   - Edit if needed

2. **Manually Post**:
   - Copy Tweet text → Post to X/Twitter
   - Copy LinkedIn text → Post to LinkedIn
   - Mark Posted column as TRUE

3. **Track Performance**:
   - See which articles scored highest
   - Monitor what gets filtered out
   - Adjust score threshold if needed

## 🔄 Next Steps: Add Automated Posting

Once you're happy with the generated posts, you can add automated posting:

### Option 1: Use Linked API (Installed)

The `n8n-nodes-linked-api` node can post to LinkedIn automatically.

**Setup**:
1. Get Linked API key from: https://linked-api.com
2. Add credential in n8n
3. Add node after "Log to Google Sheets"
4. Configure to post LinkedIn text

### Option 2: Use HDW Node (Installed)

The `n8n-nodes-hdw` node supports Twitter/X and LinkedIn.

**Setup**:
1. Get HDW API key from: https://horizondatawave.com
2. Add credential in n8n
3. Add nodes for Twitter and LinkedIn
4. Configure to post generated text

### Option 3: Use HTTP Request Nodes

For direct API access:
- **Twitter API v2**: Requires Twitter Developer account
- **LinkedIn API**: Requires LinkedIn App setup

See ADVANCED-SETUP.md for detailed instructions (I can create this if needed).

## 🔧 Customization

### Change RSS Feed

Edit "RSS Feed - HN" node:
```
URL: https://your-feed.com/rss
```

### Process More Items

Edit "Pre-Process" node, line 2:
```javascript
// Change from 3 to desired number
return items.slice(0, 3).map(item => {
```

### Adjust Score Threshold

Edit "IF Filter (Score >=7)" node:
```
value2: 5  // Lower to 5 for more posts
value2: 8  // Raise to 8 for higher quality only
```

### Change AI Model

Edit OpenAI nodes:
```
- gpt-4o (better quality)
- gpt-3.5-turbo (faster, cheaper)
```

### Modify Prompts

**Scoring Prompt** (OpenAI Score node):
```
"You are a scorer for [YOUR NICHE] news.
Respond ONLY with a number 0-10."
```

**Generation Prompt** (OpenAI Generate Text node):
```
"Generate concise, engaging posts for [YOUR NICHE].
Output exactly:
Tweet: [280-char text]
LinkedIn: [longer insightful text with CTA]"
```

## 📊 Expected Results

After running the workflow, your Google Sheet should show:

```
Timestamp              | Title                    | Score | Status           | Tweet           | LinkedIn
2025-11-02T10:30:00Z  | AI Breakthrough in...    | 9     | Ready to Post    | Amazing AI...   | This breakthrough...
2025-11-02T10:30:01Z  | New JavaScript...        | 4     | Filtered         |                 |
2025-11-02T10:30:02Z  | Automation Tool...       | 8     | Ready to Post    | Check out...    | Automation is...
```

## 🐛 Troubleshooting

### "Cannot connect to Google Sheets"
- Re-authenticate Google Sheets credential
- Make sure sheet ID is correct in nodes
- Verify sheet has correct column headers

### "OpenAI API error"
- Check API key is valid
- Verify you have credits
- Check rate limits

### "No items in output"
- RSS feed may be empty
- Try different RSS feed
- Check feed URL is accessible

### "LinkedIn node not found"
- n8n may need another restart
- Run: `docker restart n8n`
- Wait 10 seconds and try again

### "Sheet columns don't match"
- Add missing headers to row 1
- Exact names: Timestamp, Title, Link, Score, Feed, Tweet, LinkedIn, Status, Posted
- Case-sensitive!

## 💰 Cost Estimate

Per workflow execution (3 articles):
- **Scoring**: 3 × $0.0001 = $0.0003
- **Text generation**: 1-2 × $0.001 = $0.001-0.002
- **Total**: ~$0.002-0.003 per run

Very affordable! 🎉

Running hourly:
- **Per day**: 24 × $0.0025 = $0.06
- **Per month**: 30 × $0.06 = $1.80

## 🎯 Success Criteria

Your setup is successful when:
- [x] Workflow executes without errors
- [x] Google Sheet gets new rows
- [x] High-score items have generated text
- [x] Low-score items are logged without text
- [x] All columns are populated correctly

## 📈 Next Enhancements

Once this works:

1. **Add Scheduling**:
   - Replace Manual Trigger with Cron
   - Set to run hourly, daily, etc.

2. **Add More RSS Feeds**:
   - Duplicate RSS Feed node
   - Add Merge node
   - Process multiple sources

3. **Add Deduplication**:
   - Read existing sheet data
   - Filter out already-posted URLs
   - Prevent duplicates

4. **Add Automated Posting**:
   - Use Linked API or HDW nodes
   - Auto-post to social media
   - Update "Posted" column

5. **Add Image Generation**:
   - Add DALL-E node
   - Generate custom images
   - Include in posts

6. **Add Notifications**:
   - Email when high-score article found
   - Slack notification
   - Discord webhook

## 🆘 Need Help?

1. Check workflow execution log in n8n
2. Review error messages
3. Check FIXES.md for common issues
4. Test each node individually

Ready to test! Import the workflow and let's see it populate your Google Sheet! 🚀
