# How to Get Your Codex API Key 🔑

This guide explains how to obtain and configure the Codex CLI to work with Antigravity Tools.

## Table of Contents
- [What is Codex?](#what-is-codex)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Getting Your API Key](#getting-your-api-key)
- [Configuring Codex with Antigravity](#configuring-codex-with-antigravity)
- [Testing Your Setup](#testing-your-setup)
- [Advanced Configuration](#advanced-configuration)
- [Troubleshooting](#troubleshooting)

## What is Codex?

**Codex** (by OpenAI) is an AI coding assistant that helps you write code through natural language prompts. The Codex CLI provides a command-line interface for interacting with code generation models.

**Note**: While OpenAI's Codex API is being phased out, the Codex CLI can still be used with OpenAI-compatible endpoints like Antigravity Tools, giving you access to powerful models like Gemini Pro through the familiar Codex interface.

## Prerequisites

Before you begin, ensure you have:

1. ✅ **Antigravity Tools installed** and running
2. ✅ **At least one Google account** added to Antigravity
3. ✅ **API Proxy started** (default: `http://localhost:8000`)
4. ✅ **Node.js 16+** installed on your system
5. ✅ **npm or yarn** package manager

## Installation

### Install Codex CLI

The Codex CLI is distributed as an npm package. Install it globally:

```bash
# Using npm
npm install -g @openai/codex-cli

# Or using yarn
yarn global add @openai/codex-cli

# Verify installation
codex --version
```

**Alternative Installation (From Source)**:

If the npm package is unavailable, you can install from the GitHub repository:

```bash
# Clone the repository
git clone https://github.com/openai/openai-codex-cli.git
cd openai-codex-cli

# Install dependencies
npm install

# Link globally
npm link

# Verify
codex --version
```

## Getting Your API Key

### Option 1: Use Antigravity's API Key (Recommended)

The simplest method is to use the API key from your Antigravity Tools installation:

1. **Open Antigravity Tools**
2. Navigate to **API Proxy** page
3. Find the **API Key** field (default: `sk-antigravity`)
4. Copy this key - this is your "Codex API Key"

You can also change it to something more memorable:
- Click **Edit** next to API Key
- Enter a new key (e.g., `sk-my-custom-key`)
- Click **Save Configuration**

### Option 2: Retrieve from Configuration File

If you're running Antigravity in headless/Docker mode:

```bash
# macOS/Linux
cat ~/.antigravity_tools/gui_config.json | grep api_key

# Or using jq for clean output
cat ~/.antigravity_tools/gui_config.json | jq -r '.api_key'

# Docker
docker logs antigravity-manager 2>&1 | grep -i "api_key"
```

### Option 3: Generate Your Own

The API key can be any string you want! It's just used for authentication:

1. Choose a secure random string: `sk-my-secret-key-12345`
2. Set it in Antigravity: **API Proxy** → **API Key** → **Save**
3. Use the same key in Codex configuration

## Configuring Codex with Antigravity

### Method 1: Environment Variables (Quick Setup)

The fastest way to configure Codex:

```bash
# Set the API key
export OPENAI_API_KEY="sk-antigravity"

# Set the base URL to point to Antigravity
export OPENAI_BASE_URL="http://localhost:8000/v1"

# Optional: Set a specific model
export OPENAI_MODEL="gpt-4"

# Test it works
codex "Write a Hello World in Python"
```

Add these to your shell profile for persistence:

```bash
# For bash (~/.bashrc or ~/.bash_profile)
echo 'export OPENAI_API_KEY="sk-antigravity"' >> ~/.bashrc
echo 'export OPENAI_BASE_URL="http://localhost:8000/v1"' >> ~/.bashrc
source ~/.bashrc

# For zsh (~/.zshrc)
echo 'export OPENAI_API_KEY="sk-antigravity"' >> ~/.zshrc
echo 'export OPENAI_BASE_URL="http://localhost:8000/v1"' >> ~/.zshrc
source ~/.zshrc

# For fish (~/.config/fish/config.fish)
echo 'set -gx OPENAI_API_KEY "sk-antigravity"' >> ~/.config/fish/config.fish
echo 'set -gx OPENAI_BASE_URL "http://localhost:8000/v1"' >> ~/.config/fish/config.fish
```

### Method 2: Configuration File (Recommended)

Create a persistent configuration file:

```bash
# Create config directory if it doesn't exist
mkdir -p ~/.config/codex

# Create auth.json file
cat > ~/.config/codex/auth.json << 'EOF'
{
  "api_key": "sk-antigravity",
  "api_base": "http://localhost:8000/v1",
  "organization": null
}
EOF

# Or use config.toml (if Codex supports TOML)
cat > ~/.config/codex/config.toml << 'EOF'
[api]
key = "sk-antigravity"
base_url = "http://localhost:8000/v1"
model = "gpt-4"

[behavior]
stream = true
temperature = 0.7
max_tokens = 2000
EOF
```

### Method 3: Interactive Setup

Some versions of Codex support interactive setup:

```bash
codex configure

# Follow the prompts:
# API Key: sk-antigravity
# API Base URL: http://localhost:8000/v1
# Default Model: gpt-4
```

## Testing Your Setup

### Basic Test

```bash
# Simple prompt
codex "Say hello in 5 different languages"
```

### Code Generation Test

```bash
# Generate a function
codex "Create a Python function to calculate the Fibonacci sequence"

# Generate with file output
codex "Write a TypeScript class for a Todo item" > todo.ts
```

### Interactive Mode Test

```bash
# Start interactive session
codex

# Type your prompts
> Write a REST API endpoint in Express.js
> Add error handling to the previous code
> exit
```

### Verify It's Using Antigravity

Check that requests are going through Antigravity:

1. Open **Antigravity Tools**
2. Go to **Token Stats** page
3. Make a Codex request
4. Refresh stats - you should see the request logged

Or check the proxy logs:

```bash
# macOS/Linux
tail -f ~/.antigravity_tools/logs/proxy.log

# You should see entries like:
# [INFO] POST /v1/chat/completions from 127.0.0.1
# [INFO] Using account: your-email@gmail.com
# [INFO] Request completed in 1.2s
```

## Advanced Configuration

### Custom Model Mapping

Map Codex's model names to your preferred Gemini models:

1. Open **Antigravity Tools** → **API Proxy**
2. Scroll to **Custom Model Mapping**
3. Add mappings:

```json
{
  "codex": "gemini-2.0-flash-exp",
  "gpt-4": "gemini-2.5-pro-exp",
  "gpt-3.5-turbo": "gemini-2.0-flash-exp"
}
```

4. Click **Save Configuration**
5. Restart proxy if needed

### Configure Multiple Profiles

Create different profiles for different projects:

```bash
# Work profile
cat > ~/.codex-work << 'EOF'
export OPENAI_API_KEY="sk-work-key"
export OPENAI_BASE_URL="http://localhost:8000/v1"
export OPENAI_MODEL="gpt-4"
EOF

# Personal profile
cat > ~/.codex-personal << 'EOF'
export OPENAI_API_KEY="sk-personal-key"
export OPENAI_BASE_URL="http://localhost:8001/v1"
export OPENAI_MODEL="gpt-3.5-turbo"
EOF

# Use profiles
source ~/.codex-work
codex "Work-related prompt"

source ~/.codex-personal
codex "Personal prompt"
```

### Use with VS Code Extension

If you're using a VS Code Codex extension:

1. Install the extension from marketplace
2. Open settings (Cmd+, or Ctrl+,)
3. Search for "Codex" or "OpenAI"
4. Configure:
   ```json
   {
     "codex.apiKey": "sk-antigravity",
     "codex.apiBase": "http://localhost:8000/v1",
     "codex.model": "gpt-4"
   }
   ```

### Sync Configuration to Antigravity

Antigravity can automatically sync your Codex configuration:

1. Open **Antigravity Tools** → **Settings**
2. Enable **"Sync CLI Configurations"**
3. Select **Codex** from the list
4. Click **Sync Now**

This will:
- Read your `~/.config/codex/auth.json`
- Update it with current API key
- Set the correct base URL
- Backup the original file

## Troubleshooting

### Issue: "API key not found"

**Solution**:
```bash
# Verify environment variable is set
echo $OPENAI_API_KEY

# If empty, set it
export OPENAI_API_KEY="sk-antigravity"

# Or check config file exists
cat ~/.config/codex/auth.json
```

### Issue: "Connection refused"

**Cause**: Antigravity proxy is not running.

**Solution**:
1. Open Antigravity Tools
2. Go to API Proxy page
3. Click "Start Proxy"
4. Wait for "Running ✅" status
5. Try Codex again

### Issue: "Invalid authentication"

**Cause**: API key mismatch.

**Solution**:
```bash
# Get API key from Antigravity
cat ~/.antigravity_tools/gui_config.json | jq -r '.api_key'

# Update your Codex config
export OPENAI_API_KEY="<key from above>"

# Or update config file
vim ~/.config/codex/auth.json
```

### Issue: "Model not found"

**Cause**: Model name doesn't match Antigravity's mappings.

**Solution**:
1. In Antigravity, go to API Proxy → Custom Model Mapping
2. Add mapping for your model:
   ```json
   {
     "your-model-name": "gemini-2.5-pro-exp"
   }
   ```
3. Or use a supported model name:
   - `gpt-4`
   - `gpt-3.5-turbo`
   - `gemini-2.5-pro-exp`
   - `gemini-2.0-flash-exp`

### Issue: "Rate limit exceeded"

**Cause**: Account quota exhausted.

**Solution**:
1. Open Antigravity → Dashboard
2. Check active account quota
3. Switch to "Best Account" (one-click button)
4. Or add more accounts for load balancing

### Issue: Requests not showing in Token Stats

**Cause**: Authentication mode might be bypassing logging.

**Solution**:
1. Go to API Proxy → Settings
2. Set **Auth Mode** to "Strict" or "All Except Health"
3. Restart proxy
4. Make test request

### Debug Mode

Enable verbose logging:

```bash
# Codex debug mode
export CODEX_DEBUG=1
codex "test prompt"

# Antigravity debug logs
tail -f ~/.antigravity_tools/logs/proxy.log
```

## Additional Resources

### Official Documentation
- [OpenAI Codex Documentation](https://platform.openai.com/docs/guides/code) (archived)
- [Antigravity API Reference](./API_REFERENCE.md)
- [Antigravity Workflow Guide](./WORKFLOW_GUIDE.md)

### Related Guides
- [Integrating with Claude CLI](./docs/client_test_examples.md)
- [Model Mapping Configuration](./advanced_configuration.md)
- [Multi-Account Setup](./GETTING_STARTED.md#adding-your-first-account)

### Community Examples
- [GitHub: Codex CLI Examples](https://github.com/search?q=codex+cli+examples)
- [Using Codex with Continue.dev](https://continue.dev/docs)

## Summary

**Quick Recap**:

1. ✅ Install Codex CLI: `npm install -g @openai/codex-cli`
2. ✅ Get API Key: From Antigravity Tools → API Proxy page
3. ✅ Configure: Set `OPENAI_API_KEY` and `OPENAI_BASE_URL`
4. ✅ Test: Run `codex "Hello World in Python"`
5. ✅ Verify: Check Token Stats in Antigravity

**Key Environment Variables**:
```bash
export OPENAI_API_KEY="sk-antigravity"
export OPENAI_BASE_URL="http://localhost:8000/v1"
export OPENAI_MODEL="gpt-4"  # optional
```

**You're all set!** Enjoy using Codex with the power of Gemini Pro through Antigravity Tools! 🎉

---

**Need more help?** 
- Check the [Troubleshooting](#troubleshooting) section above
- Open an issue on [GitHub](https://github.com/lbjlaq/Antigravity-Manager/issues)
- Review the [Getting Started Guide](./GETTING_STARTED.md)
