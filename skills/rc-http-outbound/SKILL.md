---
name: rc-http-outbound
description: Use when user wants to call an external API, fetch data from the internet, get stock prices, weather, news, or any information from outside Rocket.Chat, or send data to an external service.
---

# RC HTTP Outbound

## Interfaces to read (silently)
- `node_modules/@rocket.chat/apps-engine/definition/accessors/IHttp.d.ts`

## RC specific rules — Gemini gets these wrong

**No fetch, no axios, no Node http — only IHttp accessor**
Gemini imports fetch() or axios by habit — both are blocked in the RC sandbox.
The only available methods are from IHttp — confirmed in the interface:
http.get, http.post, http.put, http.del, http.patch

**Delete is http.del() not http.delete()**
Gemini writes http.delete() — delete is a JS reserved word so the method is named del.
This is confirmed in IHttp.d.ts — http.delete() does not exist.

**Use data for JSON, content for raw strings**
Gemini puts JSON objects in content — this sends them as raw strings without proper headers.
IHttpRequest has two separate fields — read them from the interface:
data → auto-serializes to JSON with correct headers
content → raw string body only

**response.data is only set for valid JSON responses**
Gemini accesses response.data unconditionally — undefined if response is not JSON.
IHttpResponse shows both fields — read them from the interface:
data → only populated if response body is valid JSON
content → always available as raw string

**Always check statusCode before reading response**
Gemini uses response.data without checking statusCode first.
A 4xx or 5xx response still returns a response object — it never throws.
HttpStatusCode enum is in IHttp.d.ts — use it for comparisons.

**Missing networking permission fails silently**
Gemini does not add networking permission to app.json.
HTTP calls return undefined with no error thrown if permission is missing.
Always add networking permission — read the scaffolded app.json for the correct permission name.

**API keys must come from App Settings**
Gemini hardcodes API keys in source — admin cannot configure without touching code.
Store all keys and tokens in App Settings and read them at runtime.

**timeout and strictSSL are available but Gemini ignores them**
IHttpRequest has timeout and strictSSL fields — confirmed in the interface.
For external API calls always set a timeout to prevent the app hanging indefinitely.