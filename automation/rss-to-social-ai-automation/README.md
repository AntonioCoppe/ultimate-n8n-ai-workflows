# RSS to Social AI Automation

## Overview

This workflow automates the process of monitoring RSS feeds, scoring content relevance using AI, generating social media posts with custom text and images, and publishing to X (Twitter) and LinkedIn. Perfect for content creators and marketers who want to maintain an active social media presence with curated, AI-enhanced content.

## Features

- **Multi-RSS Feed Monitoring**: Aggregates content from multiple RSS feeds hourly
- **AI Content Scoring**: Uses OpenAI GPT-4o-mini to rate content relevance (0-10 scale)
- **Smart Filtering**: Only processes high-quality content (score ≥7)
- **AI-Generated Posts**: Creates platform-specific content for X and LinkedIn
- **AI-Generated Images**: Generates unique images using DALL-E 3 for each post
- **Dual Platform Publishing**: Automatically posts to both X (Twitter) and LinkedIn
- **Batch Processing**: Handles multiple posts efficiently with rate limiting

## Workflow Structure

```
Cron Trigger (Hourly)
    ↓
[RSS Feed 1] + [RSS Feed 2]
    ↓
Merge RSS Feeds
    ↓
Pre-Process Data
    ↓
OpenAI Score Content (0-10)
    ↓
Extract Score
    ↓
IF Filter (Score ≥7) → [Low score items skip to end]
    ↓
[OpenAI Generate Text] + [OpenAI Generate Image]
    ↓
Merge Text & Image
    ↓
Split In Batches
    ↓
[Post to X] + [Post to LinkedIn]
    ↓
Set Status
```

## Node Breakdown

### 1. Cron (Schedule Trigger)
- **Function**: Runs workflow every hour
- **Configuration**: Hourly interval
- **Customization**: Modify `hoursInterval` for different frequencies

### 2. RSS Feed Nodes (1 & 2)
- **Function**: Fetch articles from RSS feeds
- **Configuration**: Currently set to example URLs
- **Required Setup**: Replace with your RSS feed URLs
- **Note**: Can add more RSS feed nodes if needed

### 3. Merge RSS
- **Function**: Combines articles from all RSS feeds
- **Mode**: Append mode

### 4. Pre-Process (Code Node)
- **Function**: Standardizes RSS data structure
- **Extracts**: title, summary, link, pubDate, feed name
- **Purpose**: Ensures consistent data format for AI processing

### 5. OpenAI Score
- **Function**: Rates content relevance to AI automation niche
- **Model**: gpt-4o-mini
- **Output**: Single number (0-10)
- **Temperature**: 0.1 (low for consistent scoring)
- **Max Tokens**: 5

### 6. Extract Score (Code Node)
- **Function**: Parses AI response and extracts numeric score
- **Purpose**: Prepares data for filtering

### 7. IF Filter
- **Function**: Only processes high-quality content
- **Condition**: Score ≥ 7
- **True Path**: Continue to content generation
- **False Path**: Skip to status node

### 8. OpenAI Generate Text
- **Function**: Creates platform-specific social media posts
- **Model**: gpt-4o-mini
- **Output Format**:
  - Tweet: 280 characters max
  - LinkedIn: Longer format with CTA
- **Temperature**: 0.7 (creative)
- **Max Tokens**: 300

### 9. Extract Post Text (Code Node)
- **Function**: Parses AI response and separates Tweet/LinkedIn content
- **Processing**: Regex pattern matching, text trimming

### 10. OpenAI Generate Image
- **Function**: Creates custom image for each post
- **Model**: DALL-E 3
- **Size**: 1024x1024
- **Quality**: Standard
- **Prompt**: Dynamic based on article title

### 11. Merge Text & Image
- **Function**: Combines generated text with image URL
- **Mode**: Combine mode

### 12. Split In Batches
- **Function**: Processes posts one at a time
- **Batch Size**: 1
- **Purpose**: Prevents rate limiting, ensures sequential posting

### 13. Post to X (Twitter)
- **Function**: Publishes tweet with image
- **Content**: Tweet text + link + #AIAutomation hashtag
- **Media**: Includes generated image with alt text

### 14. Post to LinkedIn
- **Function**: Publishes LinkedIn post with image
- **Content**: LinkedIn text + link
- **Visibility**: PUBLIC
- **Media**: Includes generated image

### 15. Set Status
- **Function**: Logs completion status
- **Output**: Confirmation message with article title

## Prerequisites

### Required Credentials

1. **OpenAI API**
   - API key with access to:
     - GPT-4o-mini (or GPT-4, GPT-3.5-turbo)
     - DALL-E 3
   - Set up in: n8n Credentials → OpenAI

2. **X (Twitter) OAuth1**
   - Consumer Key
   - Consumer Secret
   - Access Token
   - Access Token Secret
   - Set up in: n8n Credentials → Twitter OAuth1

3. **LinkedIn OAuth2**
   - Client ID
   - Client Secret
   - Set up in: n8n Credentials → LinkedIn OAuth2

### RSS Feeds
- At least one RSS feed URL (can add more)
- Publicly accessible feeds

## Setup Instructions

### 1. Import Workflow
```bash
# In n8n interface
1. Click "Import from File"
2. Select workflow.json
3. Click "Import"
```

### 2. Configure Credentials
```
1. OpenAI Score node → Add OpenAI credentials
2. OpenAI Generate Text node → Add OpenAI credentials
3. OpenAI Generate Image node → Add OpenAI credentials
4. Post to X node → Add Twitter OAuth1 credentials
5. Post to LinkedIn node → Add LinkedIn OAuth2 credentials
```

### 3. Update RSS Feed URLs
```
1. Click "RSS Feed 1" node
2. Replace URL with your RSS feed
3. Repeat for "RSS Feed 2"
4. (Optional) Add more RSS feed nodes if needed
```

### 4. Customize Scoring Prompt
```
OpenAI Score node → Messages → System message:
"You are a scorer for [YOUR NICHE] news. Respond ONLY with a number 0-10."

OpenAI Score node → Messages → User message:
"Rate relevance to [YOUR NICHE] (0-10):"
```

### 5. Customize Content Generation
```
OpenAI Generate Text node → Messages → System message:
"Generate concise, engaging posts for [YOUR NICHE]. Output exactly: Tweet: [280-char text]\nLinkedIn: [longer insightful text with CTA]"
```

### 6. Customize Image Generation
```
OpenAI Generate Image node → Prompt:
"[YOUR STYLE] image for [YOUR NICHE] news: '{{ $json.title }}'. [YOUR REQUIREMENTS]"
```

### 7. Adjust Schedule (Optional)
```
Cron node → Rule → Interval:
- Hours: Change hoursInterval value
- Or switch to specific times using cron expression
```

### 8. Test Workflow
```
1. Click "Execute Workflow" button
2. Check execution log for errors
3. Verify posts appear on X and LinkedIn
```

## Customization Options

### Change Schedule Frequency
```javascript
// Cron node options:
- Every 30 minutes: {"field": "minutes", "minutesInterval": 30}
- Every 2 hours: {"field": "hours", "hoursInterval": 2}
- Daily at 9 AM: Use cron expression "0 9 * * *"
```

### Adjust Content Quality Threshold
```javascript
// IF Filter node:
// Current: score >= 7
// More selective: score >= 8
// Less selective: score >= 6
```

### Add More RSS Feeds
```
1. Duplicate "RSS Feed 2" node
2. Update URL
3. Connect to "Merge RSS" node (input 2, 3, etc.)
```

### Change AI Models
```javascript
// For scoring (speed vs quality):
- gpt-3.5-turbo (faster, cheaper)
- gpt-4o-mini (balanced)
- gpt-4 (highest quality)

// For image generation:
- DALL-E 3: Best quality
- DALL-E 2: Faster, cheaper
```

### Modify Post Format
```javascript
// Post to X node:
text: "{{ $json.tweetText }} {{ $json.link }} #YourHashtag"

// Post to LinkedIn node:
text: "{{ $json.linkedinText }}\n\nRead more: {{ $json.link }}"
```

### Add Error Handling
```
1. Add "Error Trigger" node after each OpenAI/social node
2. Connect to notification node (Slack, Email, etc.)
3. Configure error message format
```

## Cost Considerations

### OpenAI API Costs (per execution)
- **GPT-4o-mini Scoring**: ~$0.0001 per article
- **GPT-4o-mini Text Generation**: ~$0.001 per article
- **DALL-E 3 Image**: ~$0.04 per image

**Example**: 10 articles/hour, 3 pass filter = ~$0.12/hour = ~$87/month

### Rate Limits
- **OpenAI**: 10,000 TPM (tokens per minute) on free tier
- **X API**: Varies by plan (check Twitter Developer Portal)
- **LinkedIn API**: 100 posts per day per user

### Optimization Tips
1. Increase score threshold to reduce AI-generated posts
2. Use GPT-3.5-turbo instead of GPT-4o-mini for scoring
3. Reduce Cron frequency to every 2-3 hours
4. Skip image generation (remove DALL-E node) to save ~$0.04/post

## Troubleshooting

### Issue: RSS Feed Not Loading
**Solution**:
- Verify RSS URL is accessible
- Check if RSS feed requires authentication
- Try different RSS reader node settings

### Issue: Low Scores for All Content
**Solution**:
- Review and adjust scoring prompt
- Make prompt more specific to your niche
- Lower score threshold in IF node

### Issue: Post Text Not Formatted Correctly
**Solution**:
- Check "Extract Post Text" code node regex patterns
- Adjust OpenAI prompt to be more explicit about format
- Add debug "Set" node to inspect raw AI output

### Issue: Image Not Appearing in Posts
**Solution**:
- Verify DALL-E API access in OpenAI account
- Check image URL is valid in execution log
- Ensure social platform credentials have media upload permissions

### Issue: Rate Limit Errors
**Solution**:
- Increase "Split In Batches" delay between items
- Add "Wait" node between social posts
- Reduce Cron frequency

### Issue: Duplicate Posts
**Solution**:
- Add deduplication logic with database/spreadsheet
- Store published article URLs
- Check before posting

## Advanced Enhancements

### 1. Add Deduplication
```
After "Merge RSS" node:
1. Add "Google Sheets" or "Database" node to read posted URLs
2. Add "Code" node to filter out already-posted articles
3. Store new URLs after successful posting
```

### 2. Add Analytics Tracking
```
After "Set Status" node:
1. Add "HTTP Request" node to analytics service
2. Track: article URL, score, posted time, platforms
3. Use for performance optimization
```

### 3. Multi-Language Support
```
In "OpenAI Generate Text" node:
Add parameter: "Generate posts in [LANGUAGE]"
```

### 4. Sentiment Analysis
```
Before "IF Filter":
1. Add OpenAI node for sentiment analysis
2. Filter out negative sentiment
3. Adjust tone in generation prompt based on sentiment
```

### 5. Hashtag Optimization
```
Add OpenAI node after "Extract Post Text":
Prompt: "Generate 3-5 relevant hashtags for: {{ $json.title }}"
Include in post text
```

## Use Cases

- **Tech News Curation**: Share latest AI/tech news automatically
- **Industry Updates**: Monitor and share industry-specific RSS feeds
- **Blog Content Promotion**: Auto-share new blog posts to social media
- **News Aggregation**: Curate news from multiple sources
- **Competitive Intelligence**: Monitor and share competitor updates
- **Event Promotion**: Auto-post about upcoming events from RSS feeds

## Security Best Practices

1. **API Keys**: Store in n8n credentials, never hardcode
2. **RSS Feeds**: Validate and sanitize feed content
3. **Rate Limiting**: Implement proper delays between API calls
4. **Error Handling**: Add error notification system
5. **Access Control**: Restrict n8n instance access
6. **Audit Logs**: Monitor workflow executions regularly

## License

This workflow is part of the Ultimate n8n AI Workflows repository.
Licensed under MIT License.

## Support

For issues, questions, or contributions:
- GitHub Issues: [ultimate-n8n-ai-workflows/issues](https://github.com/oxbshw/ultimate-n8n-ai-workflows/issues)
- Documentation: Check `/docs` folder in repository

## Version History

- **v1.0.0** (2025-11-02): Initial release
  - Hourly RSS monitoring
  - AI scoring and filtering
  - Text and image generation
  - X and LinkedIn posting

## Credits

Created as part of the Ultimate n8n AI Workflows collection.
