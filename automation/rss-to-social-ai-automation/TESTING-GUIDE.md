# Testing Guide - RSS to Social AI Automation

## Quick Start

### Step 1: Import the Simple Test Workflow

Use **`workflow-simple-test.json`** - this is the easiest version to test!

**Why this version?**
- ✅ No social media credentials needed
- ✅ No LinkedIn/Twitter nodes (compatibility issue avoided)
- ✅ Only requires OpenAI API key
- ✅ Shows you exactly what would be posted
- ✅ Uses real RSS feed (Hacker News)

### Step 2: Add OpenAI Credentials

1. In n8n, go to **Credentials** → **Add Credential**
2. Search for "OpenAI"
3. Add your API key from OpenAI
4. Name it: `OpenAI Account`

### Step 3: Execute the Workflow

1. Open the imported workflow
2. Click **"Execute Workflow"** button
3. Wait 10-30 seconds for completion

### Step 4: Check Results

You'll see two output nodes:

**Final Output** (green path) - Items that passed (score ≥7):
```
✅ Ready to post: [Article Title]
- Score: 8
- Tweet: [Generated 280-char tweet]
- LinkedIn: [Generated LinkedIn post]
- Link: [Original URL]
```

**Filtered Items** (red path) - Items that didn't pass:
```
❌ Filtered out (score: 5): [Article Title]
- Score: 5
- Link: [Original URL]
```

## What This Tests

This simple version validates:
1. ✅ RSS feed reading works
2. ✅ Data preprocessing works
3. ✅ OpenAI scoring works (0-10 rating)
4. ✅ Filtering logic works (score >=7)
5. ✅ OpenAI text generation works
6. ✅ Post text extraction works
7. ✅ Final data structure is correct

## Understanding the Results

### Good Signs ✅
- You see 2 items processed
- Scores are between 0-10
- At least one item has generated tweet/LinkedIn text
- Text looks relevant to the article

### Potential Issues ⚠️

**Issue: All items filtered out (all scores < 7)**
- This is actually normal! HN articles may not be AI automation related
- Lower the threshold in the IF node to 5 or 3 to see more results
- Or wait and try again - feed content changes

**Issue: Tweet/LinkedIn text is empty**
- Check the "Extract Post Text" node output
- Look at the "generatedContent" field - is the format correct?
- The AI should output: "Tweet: [text]\nLinkedIn: [text]"

**Issue: OpenAI errors**
- Check your API key is valid
- Check you have credits in your OpenAI account
- Verify permissions include /v1/chat/completions

## Next Steps After Testing

Once this works, you have options:

### Option 1: Just Log Posts (No Social Posting)
Keep using this workflow and:
- Add a Google Sheets node to log posts
- Add an Email node to get notifications
- Add a Webhook node to send to another service

### Option 2: Add Social Posting with HTTP Requests
Replace the "Final Output" node with:
- HTTP Request to Twitter API (requires Twitter API v2)
- HTTP Request to LinkedIn API (requires LinkedIn app setup)

### Option 3: Use n8n Community Nodes
Install community nodes:
```bash
docker exec n8n npm install n8n-nodes-linkedin
docker exec n8n npm install n8n-nodes-twitter
# Then restart n8n container
```

## Troubleshooting

### "Cannot find module" errors
The workflow uses standard n8n nodes. If you get module errors:
1. Check n8n version: should be 1.0+
2. Update n8n: `docker pull n8nio/n8n:latest`
3. Restart container: `docker restart n8n`

### "Credential not found" errors
1. Make sure credential is named exactly: `OpenAI Account`
2. Or edit the OpenAI nodes to select your credential from dropdown

### "RSS feed failed" errors
The workflow uses Hacker News RSS which should always work.
If it fails:
1. Check your internet connection
2. Try URL directly: https://hnrss.org/frontpage
3. Replace with another RSS feed in the node

### OpenAI node version issues
The workflow uses OpenAI node v1.4 which should work with n8n 1.117.3.
If you get version errors, the node will auto-update when you import.

## Cost Estimate

For this test (2 items):
- **Scoring**: 2 × $0.0001 = $0.0002
- **Text generation**: 1-2 × $0.001 = $0.001-0.002
- **Total**: ~$0.002 per test run

Very cheap for testing! 🎉

## Modifying the Test

### Test with Different RSS Feed
Edit "RSS Feed - HN" node:
```
URL: https://example.com/your-feed.xml
```

### Test More Items
Edit "Pre-Process" node, line 2:
```javascript
// Change from 2 to 5
return items.slice(0, 5).map(item => {
```

### Change Score Threshold
Edit "IF Filter" node:
```
value2: 5  // Instead of 7
```

### Change AI Model
Edit OpenAI nodes:
```
Instead of gpt-4o-mini:
- gpt-4o (better quality, more expensive)
- gpt-3.5-turbo (cheaper, faster)
```

### Customize Prompts
Edit OpenAI node prompts to match your niche:

**Scoring prompt:**
```
"You are a scorer for [YOUR NICHE] news.
Respond ONLY with a number 0-10."
```

**Generation prompt:**
```
"Generate concise, engaging posts for [YOUR NICHE].
Output exactly:
Tweet: [280-char text]
LinkedIn: [longer insightful text with CTA]"
```

## Manual Testing Steps

### 1. Test RSS Feed Alone
1. Delete all nodes except Manual Trigger and RSS Feed
2. Execute
3. Verify you see article data

### 2. Test Preprocessing
1. Add back Pre-Process node
2. Execute
3. Verify data structure is clean

### 3. Test OpenAI Scoring
1. Add back OpenAI Score and Extract Score
2. Execute
3. Check scores are 0-10

### 4. Test Filtering
1. Add back IF Filter
2. Execute
3. Check true/false paths work

### 5. Test Text Generation
1. Add back OpenAI Generate Text and Extract Post Text
2. Execute
3. Verify tweet and LinkedIn text are generated

### 6. Test Full Pipeline
1. Add back Final Output and Filtered Items
2. Execute
3. Verify complete results

## Success Criteria ✅

Your test is successful when:
- [x] Workflow executes without errors
- [x] 2 articles are fetched from RSS
- [x] Each article gets a score 0-10
- [x] At least one article passes filter (or adjust threshold)
- [x] Generated text looks relevant and well-formatted
- [x] Final output shows all required fields

## What's Not Tested

This simple version does NOT test:
- ❌ Image generation (DALL-E)
- ❌ Social media posting
- ❌ Cron scheduling
- ❌ Multiple RSS feeds
- ❌ Batch processing
- ❌ Error handling for rate limits

These features need the full production version with all credentials configured.

## Ready for Production?

After successful testing, you can:
1. **Switch to Cron trigger** instead of Manual
2. **Add your RSS feeds** instead of Hacker News
3. **Add social posting** using HTTP Request nodes or community nodes
4. **Add error handling** with Error Trigger nodes
5. **Add logging** to track posted items
6. **Add deduplication** to prevent re-posting

See FIXES.md for the full production workflow structure!
