# Getting Started with Antigravity Tools 🚀

Welcome to **Antigravity Tools** - Your Personal High-Performance AI Dispatch Gateway! This guide will help you get up and running quickly.

## Table of Contents
- [What is Antigravity Tools?](#what-is-antigravity-tools)
- [System Requirements](#system-requirements)
- [Installation](#installation)
- [First-Time Setup](#first-time-setup)
- [Adding Your First Account](#adding-your-first-account)
- [Starting the API Proxy](#starting-the-api-proxy)
- [Making Your First API Call](#making-your-first-api-call)
- [Next Steps](#next-steps)

## What is Antigravity Tools?

Antigravity Tools is a desktop application that allows you to:

1. **Manage Multiple AI Accounts** - Organize Google (Gemini) and Anthropic (Claude) accounts in one place
2. **Convert Web Sessions to APIs** - Transform browser-based sessions into standard API endpoints
3. **Smart Load Balancing** - Automatically rotate between accounts based on quota availability
4. **Protocol Conversion** - Use OpenAI-compatible APIs to access Gemini and Claude services
5. **Monitor Usage** - Track quota consumption, token usage, and account health in real-time

## System Requirements

### Minimum Requirements
- **Operating System**: 
  - macOS 10.15+ (Catalina or later)
  - Windows 10/11 (64-bit)
  - Linux (Ubuntu 20.04+, Debian 11+, or equivalent)
- **RAM**: 4GB minimum (8GB recommended)
- **Disk Space**: 500MB for application + space for logs
- **Internet**: Stable internet connection required

### Supported Platforms
- **macOS**: Intel (x86_64) and Apple Silicon (M1/M2/M3)
- **Windows**: x64 architecture
- **Linux**: x64 with standard desktop environment

## Installation

### Download the Application

1. Visit the [Releases Page](https://github.com/lbjlaq/Antigravity-Manager/releases)
2. Download the appropriate installer for your platform:
   - **macOS**: `Antigravity-Tools_<version>_x64.dmg` or `_aarch64.dmg`
   - **Windows**: `Antigravity-Tools_<version>_x64-setup.exe`
   - **Linux**: `antigravity-tools_<version>_amd64.deb` or `.AppImage`

### Installation Steps

#### macOS
```bash
# Method 1: DMG Installer (Recommended)
1. Open the .dmg file
2. Drag Antigravity Tools to Applications folder
3. Open from Applications (right-click → Open if needed for unsigned builds)

# Method 2: Homebrew Cask (if available)
brew install --cask antigravity-tools
```

#### Windows
```bash
# Method 1: EXE Installer
1. Run the .exe installer
2. Follow the installation wizard
3. Launch from Start Menu or Desktop shortcut

# Method 2: Portable Version (if available)
1. Extract .zip file
2. Run Antigravity-Tools.exe directly
```

#### Linux
```bash
# Method 1: Debian/Ubuntu (.deb)
sudo dpkg -i antigravity-tools_<version>_amd64.deb
sudo apt-get install -f  # Install dependencies if needed

# Method 2: AppImage (Universal)
chmod +x Antigravity-Tools_<version>_amd64.AppImage
./Antigravity-Tools_<version>_amd64.AppImage

# Method 3: Snap (if available)
sudo snap install antigravity-tools
```

### Docker Installation (Alternative)

If you prefer running in Docker:

```bash
# Pull and run the container
docker run -d \
  --name antigravity-manager \
  -p 8000:8000 \
  -v ~/.antigravity_tools:/root/.antigravity_tools \
  -e API_KEY=sk-your-api-key \
  -e WEB_PASSWORD=your-secure-password \
  ghcr.io/lbjlaq/antigravity-manager:latest

# Access Web UI at http://localhost:8000
```

## First-Time Setup

### Launch the Application

1. **Open Antigravity Tools** from your Applications folder or Start Menu
2. You'll see the **Dashboard** page on first launch
3. The interface will be empty until you add accounts

### Understanding the Interface

The application has 6 main sections:

- **Dashboard** (🏠): Overview of all accounts and quota status
- **Accounts** (👥): Manage your AI service accounts
- **API Proxy** (🔌): Configure and control the API gateway
- **Security** (🔒): IP monitoring and access control
- **Token Stats** (📊): Usage analytics and billing insights
- **Settings** (⚙️): Application preferences and updates

### Configure Basic Settings

1. Click **Settings** (⚙️) in the sidebar
2. Set your preferences:
   - **Language**: English or 简体中文
   - **Theme**: Light or Dark mode
   - **Auto-start**: Launch on system startup (optional)
   - **Update Channel**: Stable or Beta

## Adding Your First Account

### Option 1: Add a Google Account (Gemini)

1. Navigate to **Accounts** page
2. Click **+ Add Account** button
3. Select **Google OAuth** tab
4. Choose your authentication method:

#### Automatic OAuth (Recommended)
```
1. Click "Generate OAuth URL"
2. Click "Open in Browser" 
3. Sign in to your Google account
4. Grant permissions when prompted
5. Wait for automatic redirect
6. Account will be saved automatically
```

#### Manual OAuth (Advanced)
```
1. Click "Generate OAuth URL"
2. Copy the URL
3. Open in any browser (even on different device)
4. Complete Google sign-in
5. Copy the authorization code from redirect URL
6. Paste code in app and click "I Already Authorized"
```

5. **Device Fingerprint** (Optional but Recommended):
   - Generate or import device fingerprint
   - Helps maintain stable sessions
   - Reduces 403 errors

6. Click **Save Account**

### Option 2: Add Account via Token (Advanced)

If you have existing tokens:

1. Click **+ Add Account** → **Token Import** tab
2. Paste your JSON token data:
```json
{
  "access_token": "ya29...",
  "refresh_token": "1//...",
  "scope": "...",
  "token_type": "Bearer",
  "expiry_date": 1234567890000
}
```
3. Add custom tags (optional)
4. Click **Import**

### Option 3: Batch Import (Multiple Accounts)

For importing multiple accounts at once:

1. Click **Batch Import** button
2. Choose import method:
   - **JSON Array**: Paste array of account objects
   - **From V1 Database**: Migrate from old version
3. Review accounts to be imported
4. Click **Confirm Import**

### Verify Your Account

After adding an account:

1. You'll see it appear in the **Accounts** list
2. Wait a few seconds for automatic quota fetch
3. Check that quota bars appear (Gemini Pro, Flash, etc.)
4. Green status indicator = Account is healthy
5. Yellow/Red = Check account permissions

## Starting the API Proxy

### Configure the Proxy

1. Go to **API Proxy** page
2. Configure basic settings:

```yaml
Host: 127.0.0.1        # localhost only (secure)
Port: 8000             # Default port (change if needed)
API Key: sk-antigravity  # Your authentication key
Auth Mode: Strict      # Require API key for all requests
```

3. (Optional) Advanced settings:
   - **Allow LAN Access**: Enable for network access (0.0.0.0)
   - **Custom Model Mapping**: Map model names
   - **Upstream Proxy**: Configure SOCKS5/HTTP proxy
   - **Quota Protection**: Auto-disable low-quota accounts

### Start the Proxy Server

1. Click **Start Proxy** button
2. Wait for status to show "Running ✅"
3. Note the displayed endpoints:
   ```
   OpenAI Compatible: http://localhost:8000/v1/chat/completions
   Anthropic Format:  http://localhost:8000/v1/messages
   Gemini Format:     http://localhost:8000/v1beta/models/...
   ```

### Test the Proxy

1. Click **Test Connection** button
2. You should see "Success ✓" message
3. Or use terminal:
```bash
curl http://localhost:8000/health
# Response: {"status": "ok", "version": "4.0.15"}
```

## Making Your First API Call

### Using curl

```bash
# OpenAI-Compatible Chat Completion
curl http://localhost:8000/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer sk-antigravity" \
  -d '{
    "model": "gpt-4",
    "messages": [{"role": "user", "content": "Hello!"}]
  }'
```

### Using Python

```python
import openai

# Configure client
client = openai.OpenAI(
    base_url="http://localhost:8000/v1",
    api_key="sk-antigravity"
)

# Make request
response = client.chat.completions.create(
    model="gpt-4",
    messages=[{"role": "user", "content": "Hello, world!"}]
)

print(response.choices[0].message.content)
```

### Using JavaScript/TypeScript

```javascript
import OpenAI from 'openai';

const client = new OpenAI({
  baseURL: 'http://localhost:8000/v1',
  apiKey: 'sk-antigravity'
});

const response = await client.chat.completions.create({
  model: 'gpt-4',
  messages: [{ role: 'user', content: 'Hello, world!' }]
});

console.log(response.choices[0].message.content);
```

### Using Claude CLI

```bash
# Install Claude Code CLI
npm install -g @anthropic-ai/claude-cli

# Configure
export ANTHROPIC_API_KEY="sk-antigravity"
export ANTHROPIC_BASE_URL="http://localhost:8000"

# Use it!
claude code "Create a Python script that prints Hello World"
```

### Using Codex CLI

See [How to Get Codex API Key](./CODEX_API_KEY_GUIDE.md) for detailed setup instructions.

```bash
# After setting up Codex (see guide)
export OPENAI_API_KEY="sk-antigravity"
export OPENAI_BASE_URL="http://localhost:8000/v1"

codex "Write a function to calculate fibonacci"
```

## Next Steps

### 🎯 Essential Next Steps

1. **Add More Accounts** - For better load balancing
   - Recommended: 3-5 accounts minimum
   - Mix of different quota tiers (Pro, Ultra, Free)

2. **Configure Model Mapping** - Customize routing
   - Map `gpt-4` to your preferred Gemini model
   - Set up model aliases for different use cases

3. **Set Up Monitoring** - Track usage
   - Enable **Security** → IP Monitoring
   - Review **Token Stats** daily
   - Set up quota alerts

4. **Integrate with Tools** - Connect your workflow
   - Claude Code CLI
   - Codex CLI
   - Cherry Studio
   - Continue.dev
   - Any OpenAI-compatible client

### 📚 Learn More

- [Architecture Overview](./ARCHITECTURE.md) - Deep dive into system design
- [API Reference](./API_REFERENCE.md) - Complete endpoint documentation
- [Workflow Guide](./WORKFLOW_GUIDE.md) - Advanced usage patterns
- [Codex Setup](./CODEX_API_KEY_GUIDE.md) - Get Codex working
- [Troubleshooting](./TROUBLESHOOTING.md) - Common issues and solutions

### 💡 Pro Tips

1. **Best Account Recommendation**: The dashboard shows "Best Account" based on quota. Click to switch instantly.

2. **Session Continuity**: Enable session tracking in proxy settings for conversation consistency.

3. **Quota Protection**: Set thresholds to auto-disable accounts before quota exhaustion.

4. **Background Downgrade**: Let system route background tasks to Flash models automatically.

5. **IP Whitelisting**: Add trusted IPs in Security settings for additional protection.

### 🆘 Getting Help

- **Documentation**: Read the [full documentation](./README.md)
- **GitHub Issues**: [Report bugs or request features](https://github.com/lbjlaq/Antigravity-Manager/issues)
- **Community**: Join discussions in GitHub Discussions
- **Logs**: Check `~/.antigravity_tools/logs/` for debugging

---

**🎉 Congratulations!** You're now ready to use Antigravity Tools. Happy coding! 🚀
