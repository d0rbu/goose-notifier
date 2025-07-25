# goose-notifier

**goose-notifier** is a patch for the [Goose](https://github.com/block-open-source/goose) open-source AI coding agent. It adds intelligent, LLM-powered notifications and remote control via Discord, so you can interact with Goose even when you're away from your terminal.

---

## What is Goose?

[Goose](https://github.com/block-open-source/goose) is a local, extensible AI agent that automates engineering tasks from your command line. It can:
- Build, edit, and test code with any LLM
- Run autonomously or interactively
- Integrate with your tools and workflows
- Run locally for privacy and speed

See the [Goose documentation](https://block.github.io/goose/) for more.

---

## What does goose-notifier add?

**goose-notifier** enables Goose to:
- **Send notifications to Discord** when it needs your input (e.g., next task, clarification, confirmation)
- **Receive replies**: You can respond directly in Discord, and your reply is injected into the Goose session
- **LLM-powered summaries**: Goose uses your configured LLM to summarize what it needs and suggest fallback responses if you’re away
- **Adaptive timeouts**: Learns your response patterns and adjusts notification timing
- **Smart filtering**: Skips notifications for auto-approved confirmations
- **Extensible**: Easy to add more notification providers (currently only Discord is supported)

---

## Quickstart

1. **Apply the patch to your Goose source tree**
   - This patch is for Goose CLI. Apply it to a compatible Goose checkout.

2. **Set up environment variables** (see below)

3. **Build Goose with notifications enabled:**
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

## Configuration

Set these environment variables to enable and configure notifications:

```bash
# Enable notifications
GOOSE_NOTIFICATIONS_ENABLED=true

# Timeouts (optional)
GOOSE_PING_TIMEOUT=300        # Seconds before first notification (default: 300)
GOOSE_FALLBACK_TIMEOUT=300    # Seconds before fallback (default: 300)

# Prompts directory (optional)
GOOSE_PROMPTS_DIR="crates/goose-cli/prompts"

# Discord (required)
GOOSE_DISCORD_BOT_TOKEN="your-discord-bot-token"
GOOSE_DISCORD_USER_ID="your-user-id" # Optional: defaults to any DM
```

See [README_NOTIFICATIONS.md](./README_NOTIFICATIONS.md) for full details and setup guide for the Discord bot.

---

## How it works

- When Goose is waiting for your input and you’re away, it:
  1. Uses an LLM to summarize what it needs
  2. Sends a notification to Discord (with summary and suggested reply)
  3. Listens for your reply and injects it into the session
  4. If you don’t reply, after a second timeout, Goose uses an LLM-generated fallback response to continue
- All behavior is adaptive and configurable.

---

## Security

- **Never commit your bot tokens to version control!**
- Use a `.env` file (add to `.gitignore`) or your system keychain to store secrets
- See [README_NOTIFICATIONS.md](./README_NOTIFICATIONS.md#security--best-practices) for secure setup

---

## Troubleshooting

- Run `cargo run --features notifications -- notifications` to check your config
- Common issues:
  - No notifications sent: Check `GOOSE_NOTIFICATIONS_ENABLED` and provider tokens
  - Discord errors: Check token formats and permissions
  - LLM errors: Check your LLM provider and prompt templates
- Enable debug logging:
  ```bash
  export RUST_LOG=debug
  export GOOSE_NOTIFICATIONS_DEBUG=true
  ```

---

## Contributing & More Info

- This is a patch for Goose, not a standalone project
- For Goose usage, see the [upstream Goose repo](https://github.com/block-open-source/goose)
- For notification system details, see [README_NOTIFICATIONS.md](./README_NOTIFICATIONS.md)

---

## License

Apache 2.0 (same as Goose)
