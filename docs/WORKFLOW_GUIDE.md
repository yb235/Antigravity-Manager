# Antigravity Tools - Workflow & User Guide 📘

This comprehensive guide walks you through common workflows, advanced features, and best practices for using Antigravity Tools effectively.

## Table of Contents
- [Daily Usage Workflows](#daily-usage-workflows)
- [Account Management Workflows](#account-management-workflows)
- [API Integration Workflows](#api-integration-workflows)
- [Monitoring & Optimization](#monitoring--optimization)
- [Advanced Features](#advanced-features)
- [Troubleshooting Workflows](#troubleshooting-workflows)
- [Best Practices](#best-practices)

## Daily Usage Workflows

### Morning Routine: Health Check

**Goal**: Verify all accounts are healthy and ready for the day.

```
1. Open Antigravity Tools
2. Navigate to Dashboard
3. Check indicators:
   ✅ All accounts showing green status
   ✅ Average quota > 20% for all models
   ✅ "Best Account" recommendation available
   ✅ Proxy status: "Running ✅"
4. If any issues:
   → Red account: Click to view details, may need re-authorization
   → Low quota: Review Token Stats to identify heavy usage
   → Proxy stopped: Click "Start Proxy" on API Proxy page
```

### Quick Account Switch

**Goal**: Switch to best available account when quota is low.

```
Method 1: One-Click Switch (Recommended)
1. Dashboard → "Best Account" card
2. Click "Switch to This Account" button
3. Confirmation toast appears
4. New account is now active

Method 2: Manual Switch
1. Accounts page
2. Find account with high quota
3. Click three-dot menu → "Set as Active"
4. Confirm selection
```

### Check Today's Usage

**Goal**: Review API usage and costs for the current day.

```
1. Navigate to Token Stats page
2. Select "Today" tab
3. Review metrics:
   - Total tokens consumed (input + output)
   - Requests per hour (chart)
   - Breakdown by model
   - Breakdown by account
4. Export data (optional):
   → Click "Export CSV" button
   → Save for billing/tracking
```

## Account Management Workflows

### Adding Multiple Accounts (Batch)

**Goal**: Add 5+ accounts quickly for better load balancing.

```
Step 1: Prepare Authorization
1. Accounts → Click "+ Add Account"
2. Generate OAuth URLs for all accounts
3. Copy all URLs to a text file

Step 2: Authorize All Accounts
1. Open incognito/private windows (5 windows)
2. Paste one URL per window
3. Sign in to each Google account
4. Grant permissions for all
5. Wait for redirect (do not close windows)

Step 3: Complete Import
1. Return to Antigravity app
2. For each authorization:
   - Check if auto-completed
   - If not, click "I Already Authorized, Continue"
3. Wait for quota sync (30 seconds)
4. Verify all accounts appear in list

Alternative: Token Import
1. If you have existing tokens (from backup)
2. Accounts → "Batch Import"
3. Paste JSON array:
   ```json
   [
     {"refresh_token": "1//...", "email": "account1@gmail.com"},
     {"refresh_token": "1//...", "email": "account2@gmail.com"}
   ]
   ```
4. Click "Import All"
```

### Organizing Accounts with Tags

**Goal**: Categorize accounts for easier management.

```
Use Cases for Tags:
- Priority: "premium", "standard", "backup"
- Purpose: "work", "personal", "testing"
- Tier: "ultra", "pro", "free"
- Project: "project-a", "project-b"

Steps:
1. Accounts page → Click account card
2. In details dialog, find "Tags" field
3. Add tags (comma-separated): premium, work
4. Click "Save"
5. Use filter bar to filter by tag

Bulk Tagging:
1. Select multiple accounts (checkbox)
2. Click "Bulk Actions" → "Add Tags"
3. Enter tags to add
4. Apply to all selected
```

### Device Fingerprint Management

**Goal**: Maintain stable sessions by using consistent device fingerprints.

```
What is Device Fingerprint?
- Unique identifier for your browser/device
- Helps Google recognize "same device"
- Reduces 403 errors and session invalidations

Option 1: Generate New Fingerprint
1. Add Account dialog → "Device Fingerprint" section
2. Click "Generate Fingerprint"
3. System creates random but valid fingerprint
4. Save with account

Option 2: Import from Browser
1. Get fingerprint from your browser console:
   ```javascript
   // In browser console (Chrome/Firefox)
   navigator.userAgent
   ```
2. Add Account → "Import Fingerprint"
3. Paste values
4. Save

Option 3: Share Fingerprint Across Accounts
1. Accounts → Select account with working fingerprint
2. Copy fingerprint (in details)
3. Edit other accounts
4. Paste same fingerprint
5. All accounts appear as "same device"
```

### Quota Monitoring & Alerts

**Goal**: Get notified before quota exhaustion.

```
Enable Quota Protection:
1. API Proxy page → "Advanced Settings"
2. Find "Quota Protection" section
3. Configure thresholds:
   - Low Quota Threshold: 10%
   - Auto-Disable: Yes
   - Alert Email: your@email.com (if supported)
4. Save configuration

Manual Monitoring:
1. Dashboard → Check "Average Quota" cards
2. Green (>50%): Healthy
3. Yellow (20-50%): Warning
4. Red (<20%): Critical - switch or add accounts

Set Up Scheduled Warmup:
1. API Proxy → "Scheduled Warmup"
2. Enable: Yes
3. Schedule: Daily at 3 AM
4. Purpose: Pre-fetch quotas so dashboard is accurate
```

## API Integration Workflows

### Integrating with Claude Code CLI

**Goal**: Use Claude Code with Gemini Pro via Antigravity.

```
Step 1: Install Claude Code
npm install -g @anthropic-ai/claude-cli

Step 2: Configure Environment
export ANTHROPIC_API_KEY="sk-antigravity"
export ANTHROPIC_BASE_URL="http://localhost:8000"

# Add to shell profile for persistence
echo 'export ANTHROPIC_API_KEY="sk-antigravity"' >> ~/.bashrc
echo 'export ANTHROPIC_BASE_URL="http://localhost:8000"' >> ~/.bashrc
source ~/.bashrc

Step 3: Test Connection
claude code "Write a Python function to sort a list"

Step 4: Verify in Antigravity
1. Token Stats page
2. Refresh → Should see Claude Code requests
3. Check which account was used
4. Verify token counts

Advanced: Session Continuity
1. API Proxy → Enable "Session Tracking"
2. Claude Code automatically maintains conversation context
3. Same conversation → same account used
```

### Integrating with Continue.dev (VS Code)

**Goal**: Use Continue.dev extension with Antigravity backend.

```
Step 1: Install Extension
1. VS Code → Extensions
2. Search "Continue"
3. Install "Continue - AI Code Assistant"

Step 2: Configure Continue
1. Open Command Palette (Cmd+Shift+P)
2. Type "Continue: Open config.json"
3. Add configuration:
   ```json
   {
     "models": [
       {
         "title": "Gemini Pro (via Antigravity)",
         "provider": "openai",
         "model": "gpt-4",
         "apiKey": "sk-antigravity",
         "apiBase": "http://localhost:8000/v1"
       }
     ],
     "slashCommands": [
       {
         "name": "edit",
         "description": "Edit highlighted code"
       },
       {
         "name": "comment",
         "description": "Add comments to code"
       }
     ]
   }
   ```
4. Save config

Step 3: Use Continue
1. Highlight code in VS Code
2. Press Cmd+Shift+M (or Ctrl+Shift+M)
3. Type prompt: "Explain this code"
4. Continue uses Antigravity → Gemini

Step 4: Monitor Usage
1. Antigravity → Token Stats
2. See requests from Continue
3. Check cost/performance
```

### Integrating with OpenAI Python SDK

**Goal**: Drop-in replacement for OpenAI API.

```python
# install.py - One-time setup
import subprocess
subprocess.run(["pip", "install", "openai"])

# main.py - Your application
import openai

# Configure client
client = openai.OpenAI(
    base_url="http://localhost:8000/v1",
    api_key="sk-antigravity"  # From Antigravity settings
)

# Use as normal OpenAI
def chat_with_ai(prompt: str) -> str:
    response = client.chat.completions.create(
        model="gpt-4",  # Maps to Gemini Pro
        messages=[
            {"role": "system", "content": "You are a helpful assistant."},
            {"role": "user", "content": prompt}
        ],
        temperature=0.7,
        max_tokens=2000
    )
    return response.choices[0].message.content

# Test it
if __name__ == "__main__":
    result = chat_with_ai("Explain quantum computing in simple terms")
    print(result)
    
# Verify in Antigravity:
# 1. Check Token Stats page
# 2. Should see request with ~2000 tokens
# 3. Verify account used and cost
```

### Setting Up Custom Model Mappings

**Goal**: Map familiar model names to your preferred Gemini models.

```
Scenario: You want "gpt-4" → Gemini 2.5 Pro, "gpt-3.5-turbo" → Gemini Flash

Step 1: Configure Mappings
1. API Proxy page → "Custom Model Mapping"
2. Click "Add Mapping"
3. Add rules:
   ```json
   {
     "gpt-4": "gemini-2.5-pro-exp",
     "gpt-4-turbo": "gemini-2.5-pro-exp",
     "gpt-3.5-turbo": "gemini-2.0-flash-exp",
     "claude-3-opus": "gemini-2.5-pro-exp",
     "claude-3-sonnet": "gemini-2.0-flash-exp"
   }
   ```
4. Click "Save Configuration"
5. Restart proxy (if needed)

Step 2: Test Mappings
# Client code (unchanged)
response = client.chat.completions.create(
    model="gpt-4",  # Client thinks it's GPT-4
    messages=[...]
)
# But Antigravity routes to Gemini 2.5 Pro

Step 3: Verify Routing
1. Token Stats → Check "Model" column
2. Should show "gemini-2.5-pro-exp" not "gpt-4"
3. Confirms mapping is working
```

## Monitoring & Optimization

### Analyzing Usage Patterns

**Goal**: Understand usage to optimize account allocation.

```
Weekly Analysis Workflow:

1. Token Stats → "Last 7 Days" view
2. Identify patterns:
   
   Pattern 1: Uneven Account Usage
   Problem: One account at 90%, others at 10%
   Solution:
   - Disable overused account temporarily
   - Force rotation to underutilized accounts
   - Add more accounts if total usage high

   Pattern 2: Peak Hours
   Problem: Most requests 9 AM - 5 PM
   Solution:
   - Schedule warmup at 8:30 AM
   - Pre-warm high-quota accounts
   - Set up backup accounts for peak times

   Pattern 3: Model Preference
   Problem: 80% requests go to Pro, Flash underutilized
   Solution:
   - Review if Pro is necessary for all requests
   - Adjust model mappings
   - Route background tasks to Flash

3. Export data for deeper analysis:
   - Click "Export CSV"
   - Open in Excel/Google Sheets
   - Create pivot tables by hour/model/account

4. Set optimization goals:
   - Target: Balance usage across accounts
   - Target: <50% quota usage per account
   - Target: Cost per request < $X
```

### Setting Up IP Monitoring

**Goal**: Track who's accessing your proxy and block unauthorized IPs.

```
Enable IP Monitoring:
1. Security page
2. Enable "IP Monitoring": ON
3. Choose mode:
   - Whitelist: Only allow specific IPs
   - Blacklist: Block specific IPs
   - Mixed: Whitelist + Blacklist

Whitelist Mode (Recommended for Personal Use):
1. Add your IPs:
   - Home IP: 203.0.113.50
   - Office IP: 198.51.100.25
   - VPN IP: 192.0.2.100
2. Click "Add to Whitelist"
3. Any other IP will be rejected

Blacklist Mode (For Public Access):
1. Monitor "Recent Requests" table
2. Identify abusive IPs (high request rate)
3. Select IP → "Add to Blacklist"
4. That IP is now blocked

Review Access Logs:
1. Security → "Request Log" tab
2. See:
   - IP address
   - Endpoint accessed
   - Response time
   - Status code (200, 401, 429, etc.)
3. Sort by frequency to find suspicious activity
```

### Performance Optimization

**Goal**: Reduce latency and improve response times.

```
Optimization Checklist:

□ 1. Enable Streaming
   - API Proxy → "Force Streaming": ON
   - Even non-stream requests use streaming internally
   - Benefit: Faster first-token latency

□ 2. Reduce Hop Count
   - If using upstream proxy (SOCKS5), disable if not needed
   - Direct connection to Gemini is faster
   - Exception: Required for regional restrictions

□ 3. Account Health
   - Accounts → Refresh quotas
   - Disable accounts with <5% quota
   - Ensures requests go to healthy accounts
   - Avoids retry overhead

□ 4. Model Selection
   - Use Flash for simple tasks (2-3x faster than Pro)
   - Reserve Pro for complex reasoning
   - Adjust model mappings accordingly

□ 5. Connection Pooling
   - Already enabled by default
   - Verify: Settings → "Max Connections": 10
   - Increase if handling many concurrent requests

□ 6. Quota Protection
   - Enable auto-disable at 10% threshold
   - Prevents wasted requests to exhausted accounts
   - Reduces 429 errors

Benchmark Your Setup:
# test_latency.sh
for i in {1..10}; do
  time curl -X POST http://localhost:8000/v1/chat/completions \
    -H "Authorization: Bearer sk-antigravity" \
    -d '{"model":"gpt-4","messages":[{"role":"user","content":"Hi"}]}'
done
# Look for consistent <2s response times
```

## Advanced Features

### Background Request Downgrade

**Goal**: Automatically route low-priority requests to cheaper models.

```
What It Does:
- Detects background tasks (e.g., Claude Code title generation)
- Automatically routes to Flash instead of Pro
- Saves quota for user-facing requests

Enable Feature:
1. API Proxy → "Smart Routing" section
2. Enable "Background Downgrade": ON
3. Configure rules:
   - Detect: requests with <50 tokens input
   - Detect: requests from CLI tools (User-Agent)
   - Action: Route to gemini-2.0-flash-exp
4. Save

Verify It's Working:
1. Use Claude Code: `claude code "test"`
2. Token Stats → Check requests
3. Should see:
   - Main request: gemini-2.5-pro-exp
   - Title generation: gemini-2.0-flash-exp (auto-routed)
```

### Multi-Port Configuration

**Goal**: Run multiple proxy instances with different configurations.

```
Use Case: Separate ports for different projects/teams

Configuration:
1. API Proxy → "Port Configuration"
2. Add additional ports:
   - Port 8000: Default (all models)
   - Port 8001: Pro models only (for critical work)
   - Port 8002: Flash models only (for testing)
3. Set per-port API keys:
   - 8000: sk-main-key
   - 8001: sk-premium-key
   - 8002: sk-test-key
4. Save & restart proxy

Usage:
# Team A (critical work)
export OPENAI_BASE_URL="http://localhost:8001/v1"
export OPENAI_API_KEY="sk-premium-key"

# Team B (testing)
export OPENAI_BASE_URL="http://localhost:8002/v1"
export OPENAI_API_KEY="sk-test-key"

Monitoring:
- Token Stats shows port in metadata
- Filter by port to see team usage
- Bill teams separately based on port
```

### Scheduled Quota Warmup

**Goal**: Pre-fetch quotas daily so dashboard is always accurate.

```
Why Warmup?
- Quota fetching takes 5-10 seconds per account
- With 10 accounts, 50-100 seconds total
- Warmup does this in background

Setup:
1. API Proxy → "Scheduled Tasks"
2. Enable "Quota Warmup": ON
3. Schedule: 
   - Time: 03:00 (3 AM)
   - Frequency: Daily
   - Include inactive accounts: No
4. Save

Manual Warmup:
1. API Proxy → "Actions" dropdown
2. Click "Warm Up All Accounts"
3. Background job starts
4. Toast notification on completion

Benefits:
- Dashboard always shows fresh data
- No delay when opening app in morning
- Proactive detection of expired accounts
```

### Integration with Cloudflare Tunnel

**Goal**: Expose your local Antigravity to the internet securely.

```
Why Use Cloudflare Tunnel?
- Access your Antigravity from anywhere
- No port forwarding needed
- HTTPS encryption
- DDoS protection

Setup:
1. Install cloudflared:
   ```bash
   # macOS
   brew install cloudflare/cloudflare/cloudflared
   
   # Linux
   wget https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb
   sudo dpkg -i cloudflared-linux-amd64.deb
   ```

2. Authenticate:
   ```bash
   cloudflared tunnel login
   # Opens browser, authorize with Cloudflare account
   ```

3. Create tunnel:
   ```bash
   cloudflared tunnel create antigravity-proxy
   # Note the tunnel ID shown
   ```

4. Configure tunnel:
   ```bash
   # Create config file: ~/.cloudflared/config.yml
   tunnel: <tunnel-id>
   credentials-file: /home/user/.cloudflared/<tunnel-id>.json
   
   ingress:
     - hostname: antigravity.yourdomain.com
       service: http://localhost:8000
     - service: http_status:404
   ```

5. Add DNS record:
   ```bash
   cloudflared tunnel route dns antigravity-proxy antigravity.yourdomain.com
   ```

6. Run tunnel:
   ```bash
   cloudflared tunnel run antigravity-proxy
   ```

7. Use from anywhere:
   ```python
   client = openai.OpenAI(
       base_url="https://antigravity.yourdomain.com/v1",
       api_key="sk-antigravity"
   )
   ```

Security:
1. Enable strict auth mode in Antigravity
2. Use strong API key
3. Enable IP whitelist in Cloudflare
4. Monitor access logs
```

## Troubleshooting Workflows

### Issue: Account Shows 403 Error

**Problem**: Account marked with 403 badge, not being used for requests.

```
Diagnosis Steps:
1. Accounts page → Click affected account
2. Check error details
3. Common causes:
   - Session expired
   - Google detected unusual activity
   - Device fingerprint mismatch
   - Account suspended

Solution 1: Re-authenticate
1. Click "Re-authorize" button
2. Complete OAuth flow again
3. Wait for quota sync
4. Check if 403 cleared

Solution 2: Device Fingerprint
1. Generate new fingerprint
2. Or import from working browser
3. Save account
4. Test with warmup request

Solution 3: Account Cooldown
1. Disable account for 24-48 hours
2. Use other accounts
3. Re-enable and re-authorize
4. Google may have lifted restrictions

Prevention:
- Use stable device fingerprints
- Don't share accounts across many devices
- Rotate accounts (don't overuse one)
- Keep <80% quota usage
```

### Issue: High Latency / Slow Responses

**Problem**: API requests taking >5 seconds, users complaining.

```
Diagnostic Workflow:

Step 1: Identify Bottleneck
1. Token Stats → Check "Response Time" column
2. If all requests slow:
   → Network issue (check internet)
3. If specific account slow:
   → Account issue (switch account)
4. If specific model slow:
   → Model issue (use different model)

Step 2: Check Account Health
1. Dashboard → Review quotas
2. Low quota accounts (<20%) are slower
3. Switch to "Best Account"

Step 3: Check Upstream Proxy
1. API Proxy → "Upstream Proxy" section
2. If SOCKS5 enabled, test direct connection:
   - Temporarily disable upstream proxy
   - Test latency
   - If faster, proxy is bottleneck

Step 4: Network Diagnostics
# Test direct Gemini API latency
time curl https://generativelanguage.googleapis.com/v1beta/models
# Should be <500ms

# Test Antigravity latency
time curl http://localhost:8000/health
# Should be <50ms

Step 5: Optimize Configuration
1. Enable "Force Streaming"
2. Increase max connections
3. Disable unnecessary middleware
4. Clear logs if very large

Step 6: System Resources
1. Check CPU usage (should be <50%)
2. Check RAM usage (should be <2GB)
3. Close other heavy applications
4. Restart Antigravity if memory leak suspected
```

### Issue: Requests Failing with "No Available Account"

**Problem**: All requests returning error, no accounts available.

```
Root Cause Analysis:

Check 1: All Accounts Disabled?
1. Accounts page → Check status indicators
2. If all red/disabled:
   → Re-enable at least one account
3. If all showing low quota:
   → Wait for quota reset or add new accounts

Check 2: Model Disabled on All Accounts?
1. Each account → Check "Disabled Models"
2. If "gemini-2.5-pro-exp" disabled on all:
   → Clear disabled models
3. Or use different model name

Check 3: Circuit Breakers Open?
1. API Proxy → "Circuit Breaker Status"
2. If many open circuits:
   → Wait for timeout (5 minutes default)
   → Or manually reset circuits

Check 4: Quota Protection Too Strict?
1. API Proxy → "Quota Protection"
2. If threshold set to 50%:
   → Lower to 5-10%
3. Allows accounts with some quota

Solution:
1. Add new accounts (fastest)
2. Re-authorize existing accounts
3. Wait for quota reset (check reset_at time)
4. Lower quota protection threshold
```

### Issue: Token Stats Not Updating

**Problem**: Usage not appearing in Token Stats page.

```
Troubleshooting:

Check 1: Authentication Mode
1. API Proxy → "Auth Mode"
2. If set to "Off":
   → Stats may not be recorded for all requests
3. Change to "Strict" or "All Except Health"

Check 2: Database Connection
1. Check logs: ~/.antigravity_tools/logs/proxy.log
2. Look for SQLite errors
3. If database corrupted:
   ```bash
   cd ~/.antigravity_tools
   mv proxy.db proxy.db.bak
   # Restart Antigravity (creates new DB)
   ```

Check 3: Time Filter
1. Token Stats → Check date range
2. Make sure "Today" or "This Week" selected
3. Try "All Time" to see if any data exists

Check 4: Test Request
1. Make a manual test request:
   ```bash
   curl -X POST http://localhost:8000/v1/chat/completions \
     -H "Authorization: Bearer sk-antigravity" \
     -d '{"model":"gpt-4","messages":[{"role":"user","content":"test"}]}'
   ```
2. Refresh Token Stats
3. Should appear within 5 seconds

Check 5: Proxy Running?
1. API Proxy → Status should be "Running ✅"
2. If stopped, stats won't record
3. Start proxy and retry
```

## Best Practices

### Account Management Best Practices

```
□ 1. Multiple Accounts
   - Minimum: 3-5 accounts
   - Recommended: 10+ accounts
   - Reason: Load balancing, redundancy

□ 2. Account Diversity
   - Mix of Pro and Free accounts
   - Mix of old and new accounts
   - Reason: Different quota limits

□ 3. Regular Quota Checks
   - Check daily: Dashboard view
   - Check weekly: Token Stats analysis
   - Reason: Proactive management

□ 4. Device Fingerprint Consistency
   - Use same fingerprint per account
   - Don't change frequently
   - Reason: Avoid 403 errors

□ 5. Tag Everything
   - Tag accounts by purpose/priority
   - Tag accounts by tier (free/pro)
   - Reason: Easy filtering and organization

□ 6. Backup Accounts
   - Keep 2-3 accounts unused
   - Activate only when needed
   - Reason: Emergency failover
```

### Security Best Practices

```
□ 1. Strong API Keys
   - Use random, long keys (32+ characters)
   - Never share API keys
   - Rotate every 3-6 months

□ 2. Auth Mode
   - Always use "Strict" mode in production
   - Only use "Off" for local testing
   - Reason: Prevent unauthorized access

□ 3. IP Whitelisting
   - Whitelist only trusted IPs
   - Update when IP changes
   - Review whitelist monthly

□ 4. Separate API Keys
   - Different key for Web UI vs API access
   - Web UI: WEB_PASSWORD
   - API: API_KEY
   - Reason: Limit damage if one key leaks

□ 5. Monitor Logs
   - Check Security page weekly
   - Look for unusual patterns
   - Investigate unexpected IPs

□ 6. Local Access Only
   - Bind to 127.0.0.1 (not 0.0.0.0)
   - Unless you need LAN access
   - Use Cloudflare Tunnel for remote access
```

### Cost Optimization Best Practices

```
□ 1. Model Selection
   - Use Flash for simple tasks (3x cheaper)
   - Use Pro for complex reasoning
   - Track cost per request

□ 2. Token Limits
   - Set max_tokens to reasonable values
   - Don't use 4096 if 1000 sufficient
   - Reason: Reduce unnecessary costs

□ 3. Background Downgrade
   - Enable automatic downgrade
   - Route title generation to Flash
   - Saves 70% on background tasks

□ 4. Quota Protection
   - Set thresholds at 80-90%
   - Preserve remaining quota for critical requests
   - Reason: Avoid complete exhaustion

□ 5. Usage Analysis
   - Review Token Stats weekly
   - Identify wasteful patterns
   - Optimize model mappings

□ 6. Account Tiering
   - Use free accounts for testing
   - Use Pro accounts for production
   - Monitor upgrade ROI
```

### Performance Best Practices

```
□ 1. Streaming Enabled
   - Always enable streaming
   - Even for non-stream clients
   - Benefit: Lower latency

□ 2. Connection Pooling
   - Keep default (10 connections)
   - Increase only if needed
   - Too many = resource waste

□ 3. Healthy Accounts Only
   - Disable low-quota accounts
   - Remove expired accounts
   - Benefit: Faster selection

□ 4. Minimal Middleware
   - Disable IP filter if not needed
   - Disable logging in dev mode
   - Benefit: Reduced overhead

□ 5. Local SQLite
   - Keep database on SSD
   - Vacuum periodically:
     ```bash
     sqlite3 ~/.antigravity_tools/proxy.db "VACUUM;"
     ```
   - Benefit: Faster stats queries

□ 6. Log Rotation
   - Keep logs small (<100MB)
   - Archive old logs
   - Benefit: Faster startup
```

## Summary

This guide covered:

✅ **Daily Workflows**: Health checks, account switching, usage monitoring
✅ **Account Management**: Adding, organizing, and monitoring accounts
✅ **API Integration**: Claude CLI, Continue.dev, OpenAI SDK, custom mappings
✅ **Monitoring**: Usage analysis, IP monitoring, performance optimization
✅ **Advanced Features**: Background downgrade, multi-port, Cloudflare Tunnel
✅ **Troubleshooting**: Common issues and solutions
✅ **Best Practices**: Security, cost optimization, performance tuning

**Next Steps**:
- Review [Architecture Guide](./ARCHITECTURE.md) for technical details
- Check [API Reference](./API_REFERENCE.md) for endpoint documentation
- Read [Getting Started](./GETTING_STARTED.md) for initial setup
- Visit [Codex Guide](./CODEX_API_KEY_GUIDE.md) for Codex integration

---

**Need Help?**
- GitHub Issues: [Report a bug](https://github.com/lbjlaq/Antigravity-Manager/issues)
- Discussions: [Ask questions](https://github.com/lbjlaq/Antigravity-Manager/discussions)
- Logs: `~/.antigravity_tools/logs/` for debugging

**Version**: 4.0.15 | **Last Updated**: 2024-02-03
