# rc-apps-generator

A Gemini CLI extension that helps anyone build Rocket.Chat Apps using plain English

## What it does

You describe what you want in plain English. The extension handles everything — scaffolding, code generation, validation, and packaging.
```
"hey i want a bot that greets users when they say hello"
        ↓
gemini shows a plain English plan
        ↓
you confirm or modify
        ↓
working RC app generated and packaged
```

## How it works

- **GEMINI.md** — feature map that translates plain English to RC features, always active
- **Skills** — one per RC feature, lazy loaded only when needed, reads `.d.ts` files directly from `node_modules` so knowledge never goes stale
- **rc-review skill** — universal quality gate that catches silent runtime bugs before packaging

## Skills included

| Skill | Triggers when user wants |
|---|---|
| rc-message-listener | bot that replies to messages, greet users, listen for hello |
| rc-slash-command | /command, type something to trigger an action |
| rc-persistence | save data, remember, count, store user preferences |
| rc-uikit-modal | form, popup, modal, collect input |
| rc-webhook | receive data from outside, incoming webhook |
| rc-http-outbound | call external API, fetch data, get stock prices |
| rc-app-settings | admin configurable options, API keys, tokens |
| rc-review | final review before packaging — catches silent bugs |

## Requirements

- Gemini CLI
- rc-apps CLI — installed automatically if missing