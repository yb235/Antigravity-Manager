# Antigravity Tools - Complete Documentation Index 📚

Welcome to the complete documentation for **Antigravity Tools** - Your Personal High-Performance AI Dispatch Gateway!

## 🚀 Quick Start

**New to Antigravity Tools?** Start here:

1. **[Getting Started Guide](./GETTING_STARTED.md)** ⭐
   - Installation instructions for all platforms
   - First-time setup walkthrough
   - Adding your first account
   - Making your first API call
   - **Recommended for all new users!**

2. **[Codex API Key Guide](./CODEX_API_KEY_GUIDE.md)** 🔑
   - How to get Codex CLI working
   - Step-by-step configuration
   - Troubleshooting common issues
   - **Essential if you use Codex!**

## 📖 Core Documentation

### For Users

- **[Workflow & User Guide](./WORKFLOW_GUIDE.md)** 📘
  - Daily usage workflows
  - Account management best practices
  - API integration examples
  - Monitoring and optimization
  - Advanced features
  - Troubleshooting common issues
  - **Comprehensive guide for day-to-day usage**

- **[API Reference](./API_REFERENCE.md)** 🔌
  - Complete endpoint documentation
  - Request/response formats
  - Authentication methods
  - Code examples in multiple languages
  - **For developers integrating with Antigravity**

### For Developers

- **[Architecture & Technical Overview](./ARCHITECTURE.md)** 🏗️
  - System architecture deep-dive
  - Technology stack details
  - Core components explained
  - Data flow diagrams
  - Backend and frontend architecture
  - Protocol conversion details
  - Security model
  - Performance optimizations
  - **Essential for understanding the codebase**

## 📂 Documentation Structure

```
docs/
├── GETTING_STARTED.md          # ⭐ Start here for new users
├── CODEX_API_KEY_GUIDE.md      # 🔑 Codex CLI setup guide
├── WORKFLOW_GUIDE.md           # 📘 Daily workflows & best practices
├── ARCHITECTURE.md             # 🏗️ Technical deep-dive
├── API_REFERENCE.md            # 🔌 Complete API documentation
├── advanced_configuration.md   # ⚙️ Advanced settings
├── client_test_examples.md     # 🧪 Client integration examples
├── gemini-3-image-guide.md     # 🎨 Image generation guide
├── proxy/                      # 🔐 Proxy-specific docs
│   ├── auth.md                 # Authentication modes
│   └── accounts.md             # Account lifecycle
├── zai/                        # 🤖 z.ai integration docs
│   ├── implementation.md
│   ├── mcp.md
│   ├── provider.md
│   └── vision-mcp.md
└── testing/                    # 🧪 Testing documentation
    ├── context_compression_test_plan.md
    └── ip_security_test_report.md
```

## 🎯 Documentation by Use Case

### "I want to install and start using Antigravity"
→ Read: [Getting Started Guide](./GETTING_STARTED.md)

### "I want to use Codex CLI with Antigravity"
→ Read: [Codex API Key Guide](./CODEX_API_KEY_GUIDE.md)

### "I want to integrate my application with Antigravity"
→ Read: [API Reference](./API_REFERENCE.md) + [Client Examples](./client_test_examples.md)

### "I want to understand how Antigravity works internally"
→ Read: [Architecture Overview](./ARCHITECTURE.md)

### "I want to optimize my usage and reduce costs"
→ Read: [Workflow Guide - Cost Optimization](./WORKFLOW_GUIDE.md#cost-optimization-best-practices)

### "I want to troubleshoot an issue"
→ Read: [Workflow Guide - Troubleshooting](./WORKFLOW_GUIDE.md#troubleshooting-workflows)

### "I want to set up advanced features"
→ Read: [Advanced Configuration](./advanced_configuration.md)

### "I want to generate images with Imagen 3"
→ Read: [Gemini 3 Image Guide](./gemini-3-image-guide.md)

## 🌟 Key Features Documentation

### Multi-Account Management
- **Covered in**: [Getting Started](./GETTING_STARTED.md#adding-your-first-account)
- **Advanced**: [Workflow Guide - Account Management](./WORKFLOW_GUIDE.md#account-management-workflows)
- **Technical**: [Architecture - Account Manager](./ARCHITECTURE.md#2-account-manager)

### OAuth 2.0 Authorization
- **User Guide**: [Getting Started](./GETTING_STARTED.md#option-1-add-a-google-account-gemini)
- **Technical**: [Architecture - OAuth Server](./ARCHITECTURE.md#4-oauth-server)
- **Implementation**: [Proxy Auth Docs](./proxy/auth.md)

### API Protocol Conversion
- **User Guide**: [API Reference](./API_REFERENCE.md)
- **Technical**: [Architecture - Protocol Conversion](./ARCHITECTURE.md#protocol-conversion)
- **Examples**: [Client Test Examples](./client_test_examples.md)

### Smart Load Balancing
- **User Guide**: [Workflow Guide - Account Selection](./WORKFLOW_GUIDE.md#quick-account-switch)
- **Technical**: [Architecture - Token Manager](./ARCHITECTURE.md#3-token-manager)

### Security & IP Monitoring
- **User Guide**: [Workflow Guide - IP Monitoring](./WORKFLOW_GUIDE.md#setting-up-ip-monitoring)
- **Technical**: [Architecture - Security Model](./ARCHITECTURE.md#security-model)
- **Testing**: [IP Security Test Report](./testing/ip_security_test_report.md)

### Usage Analytics
- **User Guide**: [Workflow Guide - Monitoring](./WORKFLOW_GUIDE.md#monitoring--optimization)
- **Technical**: [Architecture - Stats Collector](./ARCHITECTURE.md#7-middleware-stack)

## 🔧 Integration Guides

### Command-Line Tools
- **Claude Code CLI**: [Workflow Guide](./WORKFLOW_GUIDE.md#integrating-with-claude-code-cli)
- **Codex CLI**: [Codex API Key Guide](./CODEX_API_KEY_GUIDE.md)

### IDE Extensions
- **Continue.dev (VS Code)**: [Workflow Guide](./WORKFLOW_GUIDE.md#integrating-with-continuedev-vs-code)
- **General OpenAI-compatible**: [Client Examples](./client_test_examples.md)

### SDKs & Libraries
- **Python (OpenAI SDK)**: [Workflow Guide](./WORKFLOW_GUIDE.md#integrating-with-openai-python-sdk)
- **JavaScript/TypeScript**: [API Reference](./API_REFERENCE.md)
- **Other Languages**: [Client Examples](./client_test_examples.md)

## 📚 Reference Documentation

### Configuration Files
- **Proxy Config**: [Advanced Configuration](./advanced_configuration.md)
- **Account Storage**: [Proxy Accounts](./proxy/accounts.md)
- **Data Locations**: [Architecture - Key Configuration Files](./ARCHITECTURE.md#5-key-configuration-files)

### API Endpoints
- **Complete List**: [API Reference](./API_REFERENCE.md)
- **OpenAI Protocol**: `/v1/chat/completions`, `/v1/completions`
- **Anthropic Protocol**: `/v1/messages`
- **Gemini Protocol**: `/v1beta/models/:model/generateContent`

### Error Codes & Troubleshooting
- **Common Issues**: [Workflow Guide - Troubleshooting](./WORKFLOW_GUIDE.md#troubleshooting-workflows)
- **HTTP Status Codes**: [API Reference - Error Handling](./API_REFERENCE.md)

## 🎓 Learning Path

### Beginner (Day 1-7)
1. Read [Getting Started Guide](./GETTING_STARTED.md)
2. Install Antigravity Tools
3. Add 3-5 accounts
4. Start the proxy server
5. Make your first API call
6. Review [Workflow Guide - Daily Usage](./WORKFLOW_GUIDE.md#daily-usage-workflows)

### Intermediate (Week 2-4)
1. Read [API Reference](./API_REFERENCE.md)
2. Integrate with your preferred tool (Claude CLI, Continue.dev, etc.)
3. Set up custom model mappings
4. Enable monitoring and review usage
5. Read [Workflow Guide - Monitoring](./WORKFLOW_GUIDE.md#monitoring--optimization)

### Advanced (Month 2+)
1. Read [Architecture Overview](./ARCHITECTURE.md)
2. Optimize account allocation
3. Set up advanced features (IP monitoring, multi-port, etc.)
4. Implement Cloudflare Tunnel for remote access
5. Read [Advanced Configuration](./advanced_configuration.md)

### Expert (Ongoing)
1. Contribute to the codebase
2. Explore [z.ai integration docs](./zai/)
3. Run performance tests
4. Share your workflows with the community

## 💡 Best Practices

### Essential Reading
- **Security**: [Workflow Guide - Security Best Practices](./WORKFLOW_GUIDE.md#security-best-practices)
- **Cost Optimization**: [Workflow Guide - Cost Optimization](./WORKFLOW_GUIDE.md#cost-optimization-best-practices)
- **Performance**: [Workflow Guide - Performance Best Practices](./WORKFLOW_GUIDE.md#performance-best-practices)

### Quick Tips
- ✅ Use 10+ accounts for better load balancing
- ✅ Enable quota protection to avoid exhaustion
- ✅ Use Flash model for simple tasks (3x cheaper)
- ✅ Enable IP whitelisting for security
- ✅ Review usage stats weekly
- ✅ Keep device fingerprints consistent
- ✅ Use streaming for lower latency

## 🆘 Getting Help

### Documentation Issues
- **Found a typo?** [Open an issue](https://github.com/lbjlaq/Antigravity-Manager/issues)
- **Missing documentation?** [Request it](https://github.com/lbjlaq/Antigravity-Manager/issues)
- **Want to contribute?** [Submit a PR](https://github.com/lbjlaq/Antigravity-Manager/pulls)

### Application Issues
- **Bug report**: [GitHub Issues](https://github.com/lbjlaq/Antigravity-Manager/issues)
- **Feature request**: [GitHub Discussions](https://github.com/lbjlaq/Antigravity-Manager/discussions)
- **Question**: [GitHub Discussions](https://github.com/lbjlaq/Antigravity-Manager/discussions)

### Community
- **GitHub Discussions**: [Join the conversation](https://github.com/lbjlaq/Antigravity-Manager/discussions)
- **Sponsor**: [Support the project](https://www.buymeacoffee.com/Ctrler)

## 🔗 External Resources

### Related Projects
- **Claude Code CLI**: [Official Docs](https://docs.anthropic.com/claude/docs/claude-code)
- **OpenAI SDK**: [Python](https://github.com/openai/openai-python) | [Node.js](https://github.com/openai/openai-node)
- **Continue.dev**: [Documentation](https://continue.dev/docs)

### Background Information
- **OAuth 2.0**: [RFC 6749](https://datatracker.ietf.org/doc/html/rfc6749)
- **OpenAI API Spec**: [Official Docs](https://platform.openai.com/docs/api-reference)
- **Anthropic API**: [Official Docs](https://docs.anthropic.com/claude/reference)
- **Gemini API**: [Official Docs](https://ai.google.dev/docs)

## 📊 Documentation Statistics

- **Total Documents**: 20+ files
- **Lines of Documentation**: 10,000+
- **Code Examples**: 100+
- **Diagrams**: 5+
- **Languages Covered**: Python, JavaScript, TypeScript, Bash, Rust
- **Last Updated**: 2024-02-03

## 🎉 What's New

### Latest Documentation (v4.0.15)
- ✨ **New**: Complete Getting Started Guide
- ✨ **New**: Codex API Key Guide
- ✨ **New**: Comprehensive Workflow Guide
- ✨ **New**: Architecture Deep-Dive
- ✨ **Improved**: API Reference with more examples
- ✨ **Added**: Troubleshooting workflows
- ✨ **Added**: Best practices sections

### Coming Soon
- 🔜 Video tutorials
- 🔜 Interactive examples
- 🔜 Docker deployment guide
- 🔜 Advanced customization guide
- 🔜 Performance tuning guide

## 📝 Documentation Feedback

We're constantly improving our documentation. Please let us know:
- What's missing?
- What's confusing?
- What examples would you like to see?

**Provide feedback**: [Open an issue](https://github.com/lbjlaq/Antigravity-Manager/issues) with the label `documentation`.

---

## Quick Links

| Category | Link |
|----------|------|
| **Getting Started** | [Installation & Setup](./GETTING_STARTED.md) |
| **Codex Setup** | [API Key Guide](./CODEX_API_KEY_GUIDE.md) |
| **User Guide** | [Workflows & Best Practices](./WORKFLOW_GUIDE.md) |
| **API Docs** | [Complete Reference](./API_REFERENCE.md) |
| **Technical** | [Architecture Overview](./ARCHITECTURE.md) |
| **Advanced** | [Configuration Guide](./advanced_configuration.md) |
| **GitHub** | [Repository](https://github.com/lbjlaq/Antigravity-Manager) |
| **Issues** | [Report Bug](https://github.com/lbjlaq/Antigravity-Manager/issues) |
| **Sponsor** | [Support Project](https://www.buymeacoffee.com/Ctrler) |

---

**Welcome to Antigravity Tools!** 🚀

Start your journey with the [Getting Started Guide](./GETTING_STARTED.md) and feel free to explore other documentation as you need it.

**Version**: 4.0.15 | **License**: CC-BY-NC-SA-4.0 | **Last Updated**: 2024-02-03
