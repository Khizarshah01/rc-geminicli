---
name: rc-persistence
description: Use when user wants to save, store, remember, count, track, or retrieve data across sessions — like remembering how many times a user did something, storing user preferences, keeping a counter, or saving any information between messages or commands.
---

# RC Persistence

## Interfaces to read (silently)
- `node_modules/@rocket.chat/apps-engine/definition/accessors/IPersistence.d.ts`
- `node_modules/@rocket.chat/apps-engine/definition/accessors/IPersistenceRead.d.ts`
- `node_modules/@rocket.chat/apps-engine/definition/metadata/RocketChatAssociations.d.ts`

## RC specific rules — Gemini gets these wrong

**No plain string keys — RC requires RocketChatAssociationRecord**
Gemini writes persistence.create('my-key', data) — this does not work.
RC requires RocketChatAssociationRecord with a model type and ID string.
Import from metadata not persistence — correct path:
`@rocket.chat/apps-engine/definition/metadata`

**Read and write use different accessors**
Gemini tries to read from the persistence parameter — this fails silently.
Writing → use persistence parameter directly.
Reading → use read.getPersistenceReader() — they are separate accessors.

**readByAssociation always returns an array**
Gemini treats it as returning a single record — this breaks silently.
Always returns an array even for one match — always destructure the result.

**update throws if record does not exist**
Gemini calls update expecting upsert behavior — RC throws an error.
Always pass upsert as true for create-or-replace patterns.
upsert parameter is optional in the interface — Gemini skips it.

**Data must always be an object**
Gemini stores primitives like numbers or strings directly — RC errors out.
Always wrap primitives in an object before storing.

**Multiple associations use AND not OR**
Gemini passes multiple associations expecting any-match behavior.
RC requires ALL associations to match — intersection not union.

**Each app's data is sandboxed**
Gemini adds app-ID prefixing thinking apps share storage.
Each app only has access to its own persistent storage — no prefixing needed.