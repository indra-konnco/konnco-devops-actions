# Telegram Notification Action

[![GitHub Actions](https://img.shields.io/badge/GitHub-Actions-2088FF?logo=github-actions&logoColor=white)](https://github.com/features/actions)
[![Telegram](https://img.shields.io/badge/Telegram-2CA5E0?logo=telegram&logoColor=white)](https://telegram.org/)

A powerful and flexible GitHub Action to send rich notifications to Telegram with automatic status indicators, workflow details, and markdown formatting support.

## ✨ Features

- 🎨 **Rich Formatting**: Full Markdown/HTML support for beautiful messages
- 🎯 **Status Indicators**: Automatic emoji and status based on workflow results
- 📊 **Workflow Details**: Includes repository, branch, commit, and workflow information
- 🔗 **Direct Links**: One-click access to workflow runs
- 🔕 **Silent Mode**: Send notifications without disturbing users
- 💬 **Thread Support**: Post to specific threads in topic-enabled groups
- 🎭 **Custom Emojis**: Override default status emojis
- 🌐 **Multi-Target**: Support for private chats, groups, and channels
- 🛡️ **Error Handling**: Robust error reporting and validation

## 📋 Prerequisites

### 1. Create a Telegram Bot

1. Open Telegram and search for [@BotFather](https://t.me/BotFather)
2. Send `/newbot` command
3. Follow the prompts to create your bot
4. Save the **Bot Token** provided by BotFather

### 2. Get Your Chat ID

**For Personal Chat:**
```bash
# 1. Send a message to your bot
# 2. Visit this URL in your browser (replace YOUR_BOT_TOKEN):
https://api.telegram.org/botYOUR_BOT_TOKEN/getUpdates

# 3. Look for "chat":{"id": YOUR_CHAT_ID}
```

**For Groups:**
```bash
# 1. Add your bot to the group
# 2. Send a message in the group
# 3. Use the same URL above
# 4. Look for "chat":{"id": NEGATIVE_NUMBER} - this is your group ID
```

**For Channels:**
```bash
# 1. Add your bot as an administrator to the channel
# 2. The channel ID format is: @channelname or -100XXXXXXXXXX
```

### 3. Configure GitHub Secrets

Navigate to your repository:
```
Settings → Secrets and variables → Actions → New repository secret
```

Add the following secrets:
- `TELEGRAM_BOT_TOKEN`: Your bot token from BotFather
- `TELEGRAM_CHAT_ID`: Your chat/group/channel ID

## 🚀 Quick Start

### Basic Usage

```yaml
- name: Send Telegram notification
  uses: your-org/github-actions/telegram-notify@v1
  with:
    bot-token: ${{ secrets.TELEGRAM_BOT_TOKEN }}
    chat-id: ${{ secrets.TELEGRAM_CHAT_ID }}
    status: success
    title: Deployment Complete
    message: Application deployed successfully!
```

### Success/Failure Notifications

```yaml
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Deploy application
        run: ./deploy.sh
      
      - name: Notify on success
        if: success()
        uses: your-org/github-actions/telegram-notify@v1
        with:
          bot-token: ${{ secrets.TELEGRAM_BOT_TOKEN }}
          chat-id: ${{ secrets.TELEGRAM_CHAT_ID }}
          status: success
          title: Deployment Succeeded
      
      - name: Notify on failure
        if: failure()
        uses: your-org/github-actions/telegram-notify@v1
        with:
          bot-token: ${{ secrets.TELEGRAM_BOT_TOKEN }}
          chat-id: ${{ secrets.TELEGRAM_CHAT_ID }}
          status: failure
          title: Deployment Failed
          message: Check logs immediately!
```

## 📖 Inputs

| Input | Required | Default | Description |
|-------|----------|---------|-------------|
| `bot-token` | ✅ Yes | - | Telegram Bot Token from BotFather |
| `chat-id` | ✅ Yes | - | Telegram Chat ID (user, group, or channel) |
| `message` | ❌ No | - | Custom message content (supports Markdown) |
| `status` | ❌ No | `info` | Status type: `success`, `failure`, `warning`, `info` |
| `title` | ❌ No | - | Notification title |
| `include-details` | ❌ No | `true` | Include repository and workflow details |
| `parse-mode` | ❌ No | `Markdown` | Parse mode: `Markdown`, `MarkdownV2`, `HTML` |
| `disable-notification` | ❌ No | `false` | Send notification silently |
| `custom-emoji` | ❌ No | - | Custom emoji (overrides status emoji) |
| `thread-id` | ❌ No | - | Thread ID for topic groups |

## 📤 Outputs

| Output | Description |
|--------|-------------|
| `message-id` | ID of the sent Telegram message |
| `success` | Whether the message was sent successfully (`true`/`false`) |

## 🎨 Status Types

| Status | Emoji | Use Case |
|--------|-------|----------|
| `success` | ✅ | Successful builds, deployments, tests |
| `failure` | ❌ | Failed builds, errors, critical issues |
| `warning` | ⚠️ | Warnings, deprecations, non-critical issues |
| `info` | ℹ️ | General information, updates, notifications |

## 💡 Usage Examples

### 1. Complete CI/CD Pipeline Notifications

```yaml
name: CI/CD Pipeline

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  build-test-deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      # Notify pipeline start
      - name: Pipeline started
        uses: your-org/github-actions/telegram-notify@v1
        with:
          bot-token: ${{ secrets.TELEGRAM_BOT_TOKEN }}
          chat-id: ${{ secrets.TELEGRAM_CHAT_ID }}
          status: info
          title: Pipeline Started
          message: Running CI/CD pipeline...
          disable-notification: true
      
      - name: Install dependencies
        run: npm install
      
      - name: Run tests
        run: npm test
      
      - name: Build application
        run: npm run build
      
      - name: Deploy to production
        if: github.ref == 'refs/heads/main'
        run: ./deploy.sh production
      
      # Success notification
      - name: Success notification
        if: success()
        uses: your-org/github-actions/telegram-notify@v1
        with:
          bot-token: ${{ secrets.TELEGRAM_BOT_TOKEN }}
          chat-id: ${{ secrets.TELEGRAM_CHAT_ID }}
          status: success
          title: Pipeline Completed Successfully
          message: |
            ✨ All stages passed:
            • Dependencies installed
            • Tests passed
            • Build successful
            • Deployment complete
      
      # Failure notification
      - name: Failure notification
        if: failure()
        uses: your-org/github-actions/telegram-notify@v1
        with:
          bot-token: ${{ secrets.TELEGRAM_BOT_TOKEN }}
          chat-id: ${{ secrets.TELEGRAM_CHAT_ID }}
          status: failure
          title: Pipeline Failed
          message: |
            ❌ Pipeline failed!
            Please review the logs and fix the issues.
```

### 2. Release Notifications

```yaml
name: Release

on:
  release:
    types: [published]

jobs:
  notify:
    runs-on: ubuntu-latest
    steps:
      - name: Announce release
        uses: your-org/github-actions/telegram-notify@v1
        with:
          bot-token: ${{ secrets.TELEGRAM_BOT_TOKEN }}
          chat-id: ${{ secrets.TELEGRAM_CHAT_ID }}
          status: success
          title: New Release Published
          message: |
            🎉 **Version ${{ github.event.release.tag_name }}** is now available!
            
            **Release Notes:**
            ${{ github.event.release.body }}
            
            [Download Release](${{ github.event.release.html_url }})
          custom-emoji: 🚀
```

### 3. Security Alert Notifications

```yaml
name: Security Scan

on:
  schedule:
    - cron: '0 0 * * *'  # Daily at midnight

jobs:
  security-scan:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Run security scan
        id: scan
        run: |
          npm audit --json > audit-report.json
          VULNERABILITIES=$(cat audit-report.json | jq '.metadata.vulnerabilities.total')
          echo "vulnerabilities=$VULNERABILITIES" >> $GITHUB_OUTPUT
      
      - name: Security alert
        if: steps.scan.outputs.vulnerabilities > 0
        uses: your-org/github-actions/telegram-notify@v1
        with:
          bot-token: ${{ secrets.TELEGRAM_BOT_TOKEN }}
          chat-id: ${{ secrets.TELEGRAM_CHAT_ID }}
          status: warning
          title: Security Vulnerabilities Detected
          message: |
            ⚠️ Found ${{ steps.scan.outputs.vulnerabilities }} vulnerabilities
            
            Please review and update dependencies immediately.
          custom-emoji: 🔐
```

### 4. Multi-Environment Deployments

```yaml
name: Multi-Environment Deploy

on:
  workflow_dispatch:
    inputs:
      environment:
        description: 'Environment to deploy'
        required: true
        type: choice
        options:
          - development
          - staging
          - production

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Deploy to ${{ inputs.environment }}
        run: ./deploy.sh ${{ inputs.environment }}
      
      - name: Notify deployment
        uses: your-org/github-actions/telegram-notify@v1
        with:
          bot-token: ${{ secrets.TELEGRAM_BOT_TOKEN }}
          chat-id: ${{ secrets.TELEGRAM_CHAT_ID }}
          status: success
          title: Deployed to ${{ inputs.environment }}
          message: |
            🌍 Environment: **${{ inputs.environment }}**
            👤 Triggered by: ${{ github.actor }}
            🕐 Time: ${{ github.event.created_at }}
            
            Application is now live!
```

### 5. Custom Notification Without Details

```yaml
- name: Simple notification
  uses: your-org/github-actions/telegram-notify@v1
  with:
    bot-token: ${{ secrets.TELEGRAM_BOT_TOKEN }}
    chat-id: ${{ secrets.TELEGRAM_CHAT_ID }}
    status: warning
    title: High Memory Usage Alert
    message: |
      🔥 Server memory usage: 89%
      Action may be required soon.
    include-details: false
```

### 6. Silent Notifications for Non-Critical Updates

```yaml
- name: Background job notification
  uses: your-org/github-actions/telegram-notify@v1
  with:
    bot-token: ${{ secrets.TELEGRAM_BOT_TOKEN }}
    chat-id: ${{ secrets.TELEGRAM_CHAT_ID }}
    status: info
    title: Database Backup Completed
    message: Scheduled backup finished successfully
    disable-notification: true  # Won't trigger sound/vibration
```

### 7. Group Notifications with Threading

```yaml
- name: Post to specific thread
  uses: your-org/github-actions/telegram-notify@v1
  with:
    bot-token: ${{ secrets.TELEGRAM_BOT_TOKEN }}
    chat-id: ${{ secrets.TELEGRAM_GROUP_ID }}
    thread-id: '456'  # Specific topic thread ID
    status: success
    title: Feature Branch Merged
    message: PR #123 has been merged to main
```

### 8. HTML Formatting Example

```yaml
- name: Notification with HTML
  uses: your-org/github-actions/telegram-notify@v1
  with:
    bot-token: ${{ secrets.TELEGRAM_BOT_TOKEN }}
    chat-id: ${{ secrets.TELEGRAM_CHAT_ID }}
    status: info
    parse-mode: HTML
    message: |
      <b>Test Results:</b>
      <code>Total: 150 tests</code>
      <code>Passed: 148 ✓</code>
      <code>Failed: 2 ✗</code>
```

## 🔧 Advanced Configuration

### Using with Matrix Builds

```yaml
jobs:
  test:
    strategy:
      matrix:
        node: [16, 18, 20]
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Test on Node ${{ matrix.node }}
        run: npm test
      
      - name: Notify test results
        if: always()
        uses: your-org/github-actions/telegram-notify@v1
        with:
          bot-token: ${{ secrets.TELEGRAM_BOT_TOKEN }}
          chat-id: ${{ secrets.TELEGRAM_CHAT_ID }}
          status: ${{ job.status }}
          title: Node ${{ matrix.node }} Tests
          message: Tests completed with status: ${{ job.status }}
```

### Conditional Notifications

```yaml
- name: Notify only on main branch
  if: github.ref == 'refs/heads/main'
  uses: your-org/github-actions/telegram-notify@v1
  with:
    bot-token: ${{ secrets.TELEGRAM_BOT_TOKEN }}
    chat-id: ${{ secrets.TELEGRAM_CHAT_ID }}
    status: success
    title: Main Branch Updated
```

### Using Output in Subsequent Steps

```yaml
- name: Send notification
  id: notify
  uses: your-org/github-actions/telegram-notify@v1
  with:
    bot-token: ${{ secrets.TELEGRAM_BOT_TOKEN }}
    chat-id: ${{ secrets.TELEGRAM_CHAT_ID }}
    status: success
    title: Build Complete

- name: Log message ID
  run: |
    echo "Message ID: ${{ steps.notify.outputs.message-id }}"
    echo "Success: ${{ steps.notify.outputs.success }}"
```

## 🔐 Security Best Practices

1. **Never hardcode tokens** - Always use GitHub Secrets
2. **Limit token scope** - Use a dedicated bot for GitHub Actions
3. **Rotate tokens regularly** - Change bot tokens periodically
4. **Use environment-specific bots** - Different bots for dev/staging/prod
5. **Restrict group permissions** - Only give bots necessary permissions

## 🐛 Troubleshooting

### Message not sending

**Problem:** Action completes but no message received

**Solutions:**
- Verify bot token is correct
- Ensure chat ID is accurate (negative number for groups)
- Check bot has permission to send messages in group/channel
- Confirm bot isn't blocked by user

### Parse mode errors

**Problem:** "Can't parse entities" error

**Solutions:**
- Check markdown syntax is correct
- Escape special characters in Markdown mode: `_`, `*`, `[`, `]`, `(`, `)`, `~`, `` ` ``, `>`, `#`, `+`, `-`, `=`, `|`, `{`, `}`, `.`, `!`
- Switch to HTML parse mode if needed
- Use plain text without formatting

### Group notifications not working

**Problem:** Bot can't send to group

**Solutions:**
- Ensure bot is added to the group
- Bot must have "send messages" permission
- Use correct negative group ID format
- For channels, bot must be an administrator

### Rate limiting

**Problem:** Too many requests error

**Solutions:**
- Telegram allows 30 messages per second per bot
- Add delays between notifications if sending many
- Consider batching multiple notifications

## 📝 Message Format Examples

### Markdown Formatting

```markdown
*bold text*
_italic text_
[inline URL](http://www.example.com/)
`inline code`
```pre-formatted code block```
```

### HTML Formatting

```html
<b>bold</b>, <strong>bold</strong>
<i>italic</i>, <em>italic</em>
<a href="http://www.example.com/">inline URL</a>
<code>inline code</code>
<pre>pre-formatted code block</pre>
```

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## 📄 License

This action is available under the MIT License.

## 🆘 Support

- 📖 [Telegram Bot API Documentation](https://core.telegram.org/bots/api)
- 💬 [GitHub Discussions](https://github.com/your-org/github-actions/discussions)
- 🐛 [Report Issues](https://github.com/your-org/github-actions/issues)

## ⭐ Star History

If you find this action helpful, please consider giving it a star!

---

**Made with ❤️ for the GitHub Actions community**