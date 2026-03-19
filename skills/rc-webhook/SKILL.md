---
name: rc-webhook
description: Use when user wants to receive data from an external service, listen for events from outside Rocket.Chat, create an endpoint that other services can call, or build an incoming webhook that triggers something when data arrives.
---

# RC Webhook / API Endpoint

## Interfaces to read (silently)
- `node_modules/@rocket.chat/apps-engine/definition/api/ApiEndpoint.d.ts`
- `node_modules/@rocket.chat/apps-engine/definition/api/IApiEndpoint.d.ts`
- `node_modules/@rocket.chat/apps-engine/definition/api/IRequest.d.ts`
- `node_modules/@rocket.chat/apps-engine/definition/api/IResponse.d.ts`

## RC specific rules — Gemini gets these wrong

**Extend ApiEndpoint class — do not implement IApiEndpoint directly**
Gemini implements IApiEndpoint directly and misses success() and json() helpers.
ApiEndpoint abstract class already provides these — extend it instead.
Constructor requires the App instance — always pass this:
new MyEndpoint(this) not new MyEndpoint()

**Request body is already parsed — never call JSON.parse**
RC already parses JSON bodies into request.content as an object.
Double parsing corrupts the data — read request.content directly.

**All request headers are lowercase**
RC lowercases all header keys — request.headers['Content-Type'] always returns undefined.
Always use lowercase: request.headers['content-type']

**Private endpoint URLs include a server-generated hash**
Private endpoints have a hash segment that changes every reinstall.
Always surface the full URL dynamically — never hardcode it.
privateHash field exists in IApiRequest — read it from the interface.

**There is no built-in request authentication**
The only security option is UNSECURE — validate tokens yourself inside the handler.
authRequired only works with RC's own user auth system.

**Disabled apps return 404 automatically**
RC handles this at platform level — no custom disabled-app handling needed.