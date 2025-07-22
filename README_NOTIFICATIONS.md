# Goose Notifier: Intelligent Notification System

This document explains the notification/remote interface system added by **goose-notifier**—a patch for [Goose](https://github.com/block-open-source/goose) that lets you interact with Goose via Slack and Discord, powered by LLM-generated summaries and adaptive behavior.

---

## 🚀 What is Goose Notifier?

Goose Notifier extends Goose CLI with:
- **Slack & Discord notifications** when Goose is waiting for your input
- **LLM-powered summaries** of what Goose needs
- **Suggested fallback responses** if you’re away
- **Reply injection**: respond in Slack/Discord, and your reply is injected into the Goose session
- **Adaptive timeouts**: learns your response patterns
- **Smart filtering**: skips auto-approved confirmations
- **Extensible**: add more providers easily

> **Note:** This is a patch for Goose, not a standalone tool. You must apply it to a compatible Goose checkout.

---

## ⚡ Quick Setup

1. **Apply the patch to Goose**
2. **Set environment variables** (see below)
3. **Build with notifications:**
   ```bash
   cargo build --features notifications
   ```
4. **Run Goose as usual:**
   ```bash
   export GOOSE_NOTIFICATIONS_ENABLED=true
   cargo run --features notifications
   ```
5. **Check notification status:**
   ```bash
   cargo run --features notifications -- notifications
   # or in Goose interactive mode:
   /notifications
   ```

---

## 🔧 Environment Variables

Set these in your shell or `.env` file:

```bash
# Enable notifications
GOOSE_NOTIFICATIONS_ENABLED=true

# Timeouts (optional)
GOOSE_PING_TIMEOUT=300        # Seconds before first notification (default: 300)
GOOSE_FALLBACK_TIMEOUT=300    # Seconds before fallback (default: 300)

# Prompts directory (optional)
GOOSE_PROMPTS_DIR="crates/goose-cli/prompts"

# Slack (optional)
GOOSE_SLACK_APP_TOKEN="xapp-your-app-token"
GOOSE_SLACK_BOT_TOKEN="xoxb-your-bot-token"
GOOSE_SLACK_CHANNEL="#your-channel"  # Optional: defaults to DMs

# Discord (optional)
GOOSE_DISCORD_BOT_TOKEN="your-discord-bot-token"
GOOSE_DISCORD_USER_ID="your-user-id" # Optional: defaults to any DM
```

---

## 🛠️ Slack Bot Setup

1. Go to [api.slack.com/apps](https://api.slack.com/apps) and create a new app ("From scratch").
2. Enable **Socket Mode** and generate an app-level token (`xapp-...`).
3. Add bot token scopes:
   - `chat:write`, `chat:write.public`, `im:history`, `im:write`
4. Install the app to your workspace and copy the **Bot User OAuth Token** (`xoxb-...`).
5. (Optional) Set a default channel with `GOOSE_SLACK_CHANNEL` or use DMs.
6. Enable **Event Subscriptions** for `message.im`.
7. Set the tokens as environment variables.

---

## 🛠️ Discord Bot Setup

1. Go to [discord.com/developers/applications](https://discord.com/developers/applications) and create a new application.
2. Add a **Bot** and copy its token.
3. Enable "Message Content Intent".
4. (Optional) Get your user ID (enable Developer Mode, right-click your name, "Copy ID").
5. Invite the bot to your server or DM it directly.
6. Set the token and user ID as environment variables.

---

## 🔄 Notification Flow

1. Goose waits for your input (e.g., next task, clarification, confirmation)
2. If you don’t respond in `GOOSE_PING_TIMEOUT` seconds, Goose:
   - Uses LLM to summarize what it needs
   - Sends a notification to Slack/Discord (with summary and suggested reply)
3. You can reply in Slack/Discord; your reply is injected into Goose
4. If you don’t reply in `GOOSE_FALLBACK_TIMEOUT` seconds, Goose uses an LLM-generated fallback response to continue
5. Adaptive: If you ignore notifications, Goose pings less frequently

---

## 🧠 LLM Summaries & Fallbacks

- Goose uses your configured LLM to:
  - Summarize what it needs for notifications
  - Generate a reasonable fallback response if you’re away
- Templates for these prompts are in `crates/goose-cli/prompts/`

---

## 🔒 Security Best Practices

- **Never commit your bot tokens to version control!**
- Use a `.env` file (add to `.gitignore`) or your system keychain
- For CI/CD, use secret management (GitHub Actions secrets, Docker secrets, etc.)
- All tokens are validated at startup; connections are secured

---

## 🛠️ Troubleshooting

- Run `cargo run --features notifications -- notifications` to check config
- **No notifications sent?**
  - Check `GOOSE_NOTIFICATIONS_ENABLED` and provider tokens
  - Ensure you built with `--features notifications`
- **Slack/Discord errors?**
  - Check token formats and permissions
  - Slack: `xoxb-` for bot, `xapp-` for app
  - Discord: correct user ID, bot has DM permissions
- **LLM errors?**
  - Check your LLM provider and prompt templates
- Enable debug logging:
  ```bash
  export RUST_LOG=debug
  export GOOSE_NOTIFICATIONS_DEBUG=true
  ```

---

## 📚 More Info

- Main README: [./README.md](./README.md)
- Goose upstream: [https://github.com/block-open-source/goose](https://github.com/block-open-source/goose)
- Notification system is fully documented in this file and the main README

---

## 📝 License

Apache 2.0 (same as Goose) 