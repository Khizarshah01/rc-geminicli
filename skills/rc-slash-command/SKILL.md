---
name: rc-slash-command
description: Use when user wants to create a command that starts with /, like /hello, /price, /weather, /help, or any action triggered by typing a slash in Rocket.Chat.
---

# RC Slash Command

## Interfaces to read (silently)
- `node_modules/@rocket.chat/apps-engine/definition/slashcommands/ISlashCommand.d.ts`
- `node_modules/@rocket.chat/apps-engine/definition/accessors/IModify.d.ts`
- `node_modules/@rocket.chat/apps-engine/definition/accessors/IRead.d.ts`
- `node_modules/@rocket.chat/apps-engine/definition/accessors/IUserRead.d.ts`

## RC specific rules — Gemini gets these wrong

**Who sends the reply — clarify in plan step**
Gemini always uses context.getSender() by default — sends as admin user not bot.
Always clarify in plain english plan: "should reply come from bot or from you?"
getAppUser() returns IUser | undefined — always null check before use.

**Arguments have no quoting support**
RC splits on raw whitespace — "hello world" becomes two args not one.
Join with getArguments().join(' ') if full string is needed.

**Command names collide silently**
Generic names like help or status throw CommandAlreadyExistsError.
Always namespace: myapp-help not just help.

**Preview requires both methods**
providesPreview: true needs both previewer() AND executePreviewItem().
executor() never runs when preview item is selected — only executePreviewItem() runs.

**Preview items hard capped at 10**
RC silently discards extras with no warning.

**Trigger ID expires fast**
Open modal first, fetch data after — never the other way.

**Ephemeral vs persistent messages**
Notifier → only sender sees it, does not trigger hooks.
Message creator → visible to whole room.