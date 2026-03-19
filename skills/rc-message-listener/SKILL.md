---
name: rc-message-listener
description: Use when user wants to react to messages, auto-reply to users, greet someone when they say something, build a bot that listens and responds, trigger something when a message is sent, respond when someone types a specific word or phrase.
---

# RC Message Listener

## Interfaces to read (silently)
- `node_modules/@rocket.chat/apps-engine/definition/messages/IMessage.d.ts`
- `node_modules/@rocket.chat/apps-engine/definition/accessors/IModify.d.ts`
- `node_modules/@rocket.chat/apps-engine/definition/users/UserType.d.ts`

## RC specific rules — Gemini gets these wrong

**Listener goes on App class directly — not a separate file**
Gemini creates a standalone class like slash commands.
RC discovers listeners by checking if App implements the interface directly.

**Ephemeral notifications are loop safe**
Use notifier's user notification when reply does not need to be visible to whole room.
Notifier does NOT re-trigger message hooks — message creator does.

**DM to bot hook has no check guard**
Unlike other hooks, this one has no optional check method.
Always add manual sender check inside execute.

**Update hooks have same loop risk as send hooks**
Gemini guards send hook but forgets update hook.
Always add bot check to update handlers too.

**Pre-send extend is additive only**
Cannot overwrite message text using extender.
To rewrite content use pre-send modify with builder instead.