# Setup Guide: YouTube Content Research and AI-Powered Video Idea Generator

Complete step-by-step guide to set up the YouTube research workflow that analyzes competitors and generates 30 AI-powered video ideas.

## 📋 Table of Contents

1. [Prerequisites](#prerequisites)
2. [Apify Setup](#apify-setup)
3. [OpenAI API Configuration](#openai-api-configuration)
4. [Airtable Setup](#airtable-setup)
5. [n8n Workflow Import](#n8n-workflow-import)
6. [Testing & Validation](#testing--validation)
7. [Production Usage](#production-usage)
8. [Optimization Tips](#optimization-tips)

---

## Prerequisites

### Required Accounts

- [ ] n8n instance (Cloud v1.0+ or Self-hosted)
- [ ] Apify account (for YouTube scraping)
- [ ] OpenAI account with API access
- [ ] Airtable account (Free or paid plan)

### Budget Requirements

Estimated costs per workflow execution:
- **Apify**: $0.10-0.50 (depending on video count)
- **OpenAI**: $0.15-0.45 (GPT-4o-mini + GPT-4.1-mini)
- **Airtable**: Free tier sufficient (up to 1,200 records/base)
- **Total per run**: ~$0.25-1.00

### Estimated Setup Time

- **Quick Setup**: 45 minutes
- **Full Setup with Testing**: 1.5-2 hours

---

## Apify Setup

### What is Apify?

Apify is a web scraping and automation platform. We use it to extract YouTube video data without violating terms of service.

### Step 1: Create Apify Account

1. **Sign Up**
   ```
   - Visit: https://apify.com/
   - Click "Sign up free"
   - Use email or GitHub/Google
   - Verify email
   ```

2. **Choose Plan**
   ```
   - Free tier: $5 credit (good for testing)
   - Starter: $49/month (recommended for production)
   - Navigate to: Account → Subscription
   ```

### Step 2: Get API Token

1. **Navigate to API Settings**
   ```
   - Click your profile icon
   - Select "Settings"
   - Go to "Integrations" tab
   - Click "API tokens"
   ```

2. **Create New Token**
   ```
   - Click "Create new token"
   - Name: "n8n YouTube Research"
   - Scope: "Full access" (or limited to Actors)
   - Copy the token immediately
   - Store securely (you won't see it again)
   ```

3. **Save Token Format**
   ```
   Example: apify_api_aBcDeFgHiJkLmNoPqRsTuVwXyZ1234567890
   ```

### Step 3: Test YouTube Scraper Actor

1. **Find the Actor**
   ```
   - Go to: https://apify.com/store
   - Search: "youtube scraper"
   - Select: "streamers/youtube-scraper"
   - Or direct: https://apify.com/streamers/youtube-scraper
   ```

2. **Test Run (Optional)**
   ```
   - Click "Try for free"
   - Input a YouTube channel URL
   - Example: https://www.youtube.com/@MrBeast
   - Configure:
     ✓ Date filter: "month"
     ✓ Max results: 5
     ✓ Sorting order: "views"
   - Click "Start"
   - Wait ~30 seconds
   - Review results
   ```

3. **Verify Output Format**
   ```json
   {
     "title": "Video title",
     "viewCount": 1000000,
     "likes": 50000,
     "channelName": "Channel Name",
     "numberOfSubscribers": 10000000,
     "thumbnailUrl": "https://...",
     "url": "https://youtube.com/watch?v=...",
     "date": "2025-01-15T10:30:00.000Z"
   }
   ```

---

## OpenAI API Configuration

### Step 1: Create OpenAI Account

1. **Sign Up**
   ```
   - Visit: https://platform.openai.com/
   - Click "Sign up"
   - Verify email
   - Complete phone verification
   ```

2. **Add Payment Method**
   ```
   - Go to: Settings → Billing
   - Click "Add payment method"
   - Add credit card
   - Set up usage limits (recommended: $10/month for testing)
   ```

### Step 2: Generate API Key

1. **Create Key**
   ```
   - Navigate to: API Keys
   - Click "Create new secret key"
   - Name: "n8n YouTube Research"
   - Permissions: "All" (or restrict to specific models)
   - Copy key immediately
   ```

2. **Key Format**
   ```
   Example: sk-proj-aBcDeFgHiJkLmNoPqRsTuVwXyZ1234567890
   ```

3. **Set Usage Limits** (Recommended)
   ```
   - Settings → Limits
   - Hard limit: $10.00
   - Email alerts: Enabled
   ```

### Step 3: Verify Model Access

Check you have access to required models:

1. **GPT-4o-mini** (for thumbnail analysis)
   ```
   - Playground → Select model
   - Verify "gpt-4o-mini" is available
   - Test with image upload
   ```

2. **GPT-4.1-mini** (for idea generation)
   ```
   - Verify "gpt-4.1-mini" is in model list
   - Or use "gpt-4-turbo" as alternative
   ```

### Step 4: Test API Connection

```bash
# Using curl (optional test)
curl https://api.openai.com/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -d '{
    "model": "gpt-4o-mini",
    "messages": [{"role": "user", "content": "Hello"}]
  }'
```

---

## Airtable Setup

### Step 1: Create Airtable Account

1. **Sign Up**
   ```
   - Visit: https://airtable.com/
   - Click "Sign up for free"
   - Use email or Google/Apple
   ```

2. **Create Workspace**
   ```
   - Name: "YouTube Content Research"
   - Click "Create"
   ```

### Step 2: Create Base for Video Ideas

1. **New Base**
   ```
   - Click "+ Add a base"
   - Select "Start from scratch"
   - Name: "YouTube Video Ideas"
   ```

2. **Configure Table: "Generated Ideas"**

   | Field Name | Field Type | Options |
   |------------|------------|---------|
   | title | Single line text | Primary field |
   | concept | Long text | Rich text enabled |
   | thumbnail | Long text | - |
   | talking_points | Long text | - |
   | justification | Long text | - |
   | created_at | Created time | Auto-populated |
   | status | Single select | Options: New, Reviewed, In Production, Published |

3. **Get Base ID**
   ```
   - Click "Help" (? icon)
   - Select "API documentation"
   - Copy Base ID from URL or documentation
   - Format: appXXXXXXXXXXXXXX
   ```

4. **Get Table Name**
   ```
   - Simply copy the table name
   - Example: "Generated Ideas"
   ```

### Step 3: Create Base for Scraped Videos

1. **New Base (or add table to existing)**
   ```
   - In same workspace
   - Name: "YouTube Research Data"
   - Or add table to existing base
   ```

2. **Configure Table: "Scraped Videos"**

   | Field Name | Field Type | Options |
   |------------|------------|---------|
   | post_title | Single line text | Primary field |
   | views | Number | Integer, allow negative: No |
   | likes | Number | Integer |
   | username | Single line text | - |
   | subscribers | Number | Integer |
   | thumbnail_description | Long text | - |
   | thumbnail_url | URL | - |
   | post_url | URL | - |
   | posted_at | Date | Include time |
   | scraped_at | Date | - |
   | channel | Single line text | - |

3. **Create Views**
   ```
   - "All Videos" (default)
   - "Top Performers" (filter: views > 100000)
   - "Recent" (sort by: posted_at desc)
   ```

### Step 4: Generate API Key

1. **Create Personal Access Token**
   ```
   - Click profile icon → "Account"
   - Select "Developer" tab
   - Click "Generate token"
   - Name: "n8n YouTube Workflow"
   - Scopes:
     ✓ data.records:read
     ✓ data.records:write
     ✓ schema.bases:read
   - Add access to your bases
   - Copy token
   ```

2. **Token Format**
   ```
   Example: patXXXXXXXXXXXXXX.xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
   ```

---

## n8n Workflow Import

### Step 1: Prepare Workflow File

```bash
# If from GitHub
git clone https://github.com/yourusername/n8n-youtube-research.git
cd n8n-youtube-research

# Or download directly
# Save: "YouTube Content Research and AI-Powered Video Idea Generator.json"
```

### Step 2: Import to n8n

1. **Via UI**
   ```
   - n8n Dashboard → "Workflows"
   - Click "+ Add workflow" → "Import from file"
   - Select JSON file
   - Click "Import"
   ```

2. **Verify Import**
   ```
   - Workflow should open automatically
   - Check all nodes are visible
   - No missing node type errors
   ```

### Step 3: Configure API Keys

#### Update "Workflow Configuration" Node:

1. **Click the node**
2. **Edit assignments**:
   ```javascript
   {
     "apifyApiToken": "apify_api_YOUR_ACTUAL_TOKEN",
     "openaiApiKey": "sk-proj-YOUR_ACTUAL_KEY"
   }
   ```
3. **Save node**

#### Alternative (More Secure):

Use n8n credentials instead of hardcoding:

```javascript
// In HTTP Request nodes, reference credentials:
"authentication": "genericCredentialType",
"genericAuthType": "httpHeaderAuth"

// Then add credential with:
// Header: "Authorization"
// Value: "Bearer {{ $credentials.apiKey }}"
```

### Step 4: Update Airtable Connections

#### In "Save to Airtable" Node (Video Ideas):

1. **Add Airtable Credential**
   ```
   - Click credential dropdown
   - "Create New Credential"
   - Select "Airtable Personal Access Token"
   - Paste your token
   - Save
   ```

2. **Configure Base & Table**
   ```
   - Base: Select from dropdown (or enter ID)
   - Table: Select "Generated Ideas"
   ```

3. **Update Column Mappings**
   ```javascript
   {
     "title": "={{ $json.videoIdeas.new_video_title }}",
     "concept": "={{ $json.videoIdeas.concept_and_angle }}",
     "thumbnail": "={{ $json.videoIdeas.suggested_thumbnail_description }}",
     "talking_points": "={{ $json.videoIdeas.key_talking_points_outline }}",
     "justification": "={{ $json.videoIdeas.trend_justification }}"
   }
   ```

#### In "Save Video Data" Node:

1. **Select Base & Table**
   ```
   - Base: YouTube Research Data
   - Table: Scraped Videos
   ```

2. **Verify Mappings**
   ```javascript
   {
     "post_title": "={{ $json.title }}",
     "views": "={{ $json.viewCount }}",
     "likes": "={{ $json.likes }}",
     // ... etc
   }
   ```

### Step 5: Update OpenAI HTTP Nodes

Both "Analyze Image" and the AI agent need OpenAI credentials:

1. **"Analyze Image" Node**
   ```
   - Method: POST
   - URL: https://api.openai.com/v1/chat/completions
   - Header: Authorization = Bearer YOUR_KEY
   - Update with your actual key
   ```

2. **"OpenAI Chat Model1" Node**
   ```
   - Already configured for credentials
   - Add "OpenAI API" credential
   - Select model: gpt-4.1-mini
   ```

---

## Testing & Validation

### Pre-Flight Checklist

- [ ] All placeholders replaced
- [ ] Apify API token configured
- [ ] OpenAI API key configured
- [ ] Airtable credentials added
- [ ] Base and table IDs correct
- [ ] All nodes show no errors

### Step 1: Test Chat Trigger

1. **Get Chat URL**
   ```
   - Click "When Chat Message Received" node
   - Copy webhook URL
   - Format: https://YOUR_N8N/webhook/WEBHOOK_ID
   ```

2. **Open Chat Interface**
   ```
   - Add /chat to webhook URL
   - Example: https://YOUR_N8N/webhook/xxx-xxx/chat
   - Bookmark this URL
   ```

### Step 2: Test with Small Channel

Use a small channel for initial test:

```
Input: https://www.youtube.com/@TechQuickie
```

**Why small channel?**
- Faster scraping (fewer videos)
- Lower API costs
- Easier to validate results

### Step 3: Monitor Execution

1. **Send Chat Message**
   ```
   - Paste YouTube channel URL
   - Hit enter
   - Watch workflow execute
   ```

2. **Check Progress**
   ```
   - Open workflow in n8n
   - Go to "Executions" tab
   - Click latest execution
   - Monitor node-by-node progress
   ```

3. **Expected Timeline**
   ```
   - Apify scraping: ~90 seconds (3 runs)
   - Thumbnail analysis: ~30-60 seconds (per batch)
   - AI idea generation: ~30-60 seconds
   - Airtable saves: ~5-10 seconds
   - Total: 3-5 minutes
   ```

### Step 4: Validate Results

#### Check Airtable - Scraped Videos:

- [ ] Videos appear in table
- [ ] Thumbnail descriptions populated
- [ ] View counts accurate
- [ ] URLs working
- [ ] Dates formatted correctly

#### Check Airtable - Video Ideas:

- [ ] 30 ideas generated
- [ ] Titles are unique
- [ ] Concepts are detailed
- [ ] Thumbnail descriptions specific
- [ ] Talking points outlined
- [ ] Justifications provided

### Step 5: Quality Check

Review 5 random video ideas:

1. **Title Quality**
   - Clickable?
   - Clear topic?
   - Optimized for search?

2. **Concept Originality**
   - Unique angle?
   - Not just copying competitors?
   - Actionable?

3. **Thumbnail Strategy**
   - Specific visual suggestions?
   - Color schemes mentioned?
   - Text overlay ideas?

---

## Production Usage

### Best Practices

1. **Start with Competitor Analysis**
   ```
   - Identify 3-5 top competitors
   - Run workflow for each
   - Compare generated ideas
   - Identify patterns
   ```

2. **Regular Research Schedule**
   ```
   - Weekly: Check top 5 videos from competitors
   - Monthly: Deep dive on one major channel
   - Quarterly: Analyze emerging creators
   ```

3. **Idea Management**
   ```
   - Review ideas in Airtable
   - Mark status: New → Reviewed → In Production
   - Add notes and modifications
   - Track which ideas you implement
   ```

### Optimization Tips

1. **Reduce Costs**
   ```
   - Limit max results in runConfigs
   - Cache thumbnail analyses
   - Use GPT-3.5-turbo for initial drafts
   ```

2. **Improve Quality**
   ```
   - Customize AI system prompt
   - Add your brand voice guidelines
   - Include your niche keywords
   ```

3. **Scale Operations**
   ```
   - Process multiple channels in parallel
   - Create separate bases per niche
   - Set up automated weekly runs
   ```

### Workflow Modifications

#### Change Video Scraping Criteria:

```javascript
// In "runConfigs" node
{
  "filter": "week",    // Change to: today, week, month, year
  "results": 10,       // Adjust number
  "channelUrl": "..."
}
```

#### Adjust AI Prompt:

Edit the system message in "Video Ideas Generator Agent":

```
You are "Strategos AI", an expert YouTube Content Strategist...

[Add your modifications here]
- Focus on: [your niche]
- Tone: [your brand voice]
- Target audience: [your viewers]
```

#### Add More Data Points:

In "Save Video Data" node, add fields:
```javascript
{
  "duration": "={{ $json.duration }}",
  "comments": "={{ $json.commentCount }}",
  "description": "={{ $json.description }}"
}
```

---

## Troubleshooting

### Issue 1: Apify Timeout

**Symptoms**: Wait node completes but no data returned

**Solutions**:
```
1. Increase wait time to 120 seconds
2. Check Apify run status manually
3. Verify channel URL format
4. Try smaller channel first
```

### Issue 2: Thumbnail Analysis Fails

**Symptoms**: "Analyze Image" node returns errors

**Solutions**:
```
1. Check OpenAI API quota
2. Verify image URLs are accessible
3. Test one image URL in browser
4. Confirm GPT-4o-mini access
5. Check API key permissions
```

### Issue 3: No Ideas Generated

**Symptoms**: "Video Ideas Generator Agent" produces empty output

**Solutions**:
```
1. Review execution logs
2. Check JSON parsing in output
3. Verify GPT-4.1-mini availability
4. Test with simpler prompt
5. Check token limits
```

### Issue 4: Airtable Save Errors

**Symptoms**: "Save to Airtable" fails

**Solutions**:
```
1. Verify PAT token is valid
2. Check base and table IDs
3. Confirm field names match exactly
4. Check field types (text vs number)
5. Review API rate limits
```

### Issue 5: Chat Not Responding

**Symptoms**: Chat interface loads but doesn't process

**Solutions**:
```
1. Check webhook URL is correct
2. Verify workflow is activated
3. Test webhook endpoint directly
4. Check n8n logs for errors
5. Restart n8n instance
```

---

## Advanced Configuration

### 1. Batch Processing Multiple Channels

```javascript
// Create array of channels
const channels = [
  "https://youtube.com/@channel1",
  "https://youtube.com/@channel2",
  "https://youtube.com/@channel3"
];

// Process each with delay
```

### 2. Automated Weekly Reports

```
- Add Schedule Trigger
- Set to weekly execution
- Add email node to send summary
- Include top performing ideas
```

### 3. Custom Filtering

```javascript
// Filter videos by criteria
if (views > 1000000 && likes/views > 0.05) {
  // High engagement video
  analyze();
}
```

---

## Next Steps

1. [ ] Test workflow with sample channel
2. [ ] Analyze 3 competitor channels
3. [ ] Review generated ideas
4. [ ] Customize AI prompts
5. [ ] Implement best ideas
6. [ ] Track performance metrics

## Resources

- [Apify Documentation](https://docs.apify.com/)
- [OpenAI API Reference](https://platform.openai.com/docs/)
- [Airtable API Docs](https://airtable.com/developers/web/api)
- [n8n Community Forum](https://community.n8n.io/)

---

**Setup completed?** Start researching competitors and generating viral video ideas!

**Questions?** Open an issue or reach out to the community.
