---
name: rc-review
description: Use AFTER all Rocket.Chat App code is written and BEFORE packaging. Review every file for RC runtime failures — code that compiles clean but silently does nothing or breaks at runtime.
---

# RC App Final Review

RC apps fail in two ways — compile errors and silent runtime failures.
rc-apps validate catches compile errors. This review catches silent failures.
Go through every generated file and apply these corrections before packaging.

## Messages that are built but never sent
Gemini builds message objects and considers the job done.
In RC, building is not sending — .finish() must always be called.
Every .startMessage() must end with .finish().
Every .getMessage() must end with .finish().
A missing .finish() produces zero errors and zero output.

## Reply sent by wrong user
Gemini defaults to context.getSender() as the message sender.
This sends the reply as the admin user not a bot — wrong for most apps.
Check the plan — did user ask for bot reply or their own account?
Bot reply → getAppUser(this.getID()) — null check required, returns IUser | undefined.
User reply → getSender() is correct.

## Message listeners that loop forever
Gemini writes handlers that reply to every message including the bot's own replies.
Every IPostMessageSent handler must reject app and bot sender types as the first check.
Every IPostMessageUpdated handler needs the same check — Gemini adds it to send but forgets update.
IPostMessageSentToBot has no optional check method — manual sender ID check required inside execute.
A missing sender check causes the app to reply to itself infinitely with no error thrown.

## Modal trigger IDs that expire
Gemini fetches data from an API then tries to open a modal — the trigger ID expired during the fetch.
Trigger IDs expire in seconds. Always open the modal first, load data after.

## Persistence that throws on first run
Gemini calls updateByAssociation without upsert — RC throws because no record exists yet.
Every create-or-replace pattern needs upsert set to true as the third argument.
readByAssociation always returns an array — Gemini treats it as a single record and crashes silently.

## RC sandbox violations
RC runs apps in an isolated sandbox — Node.js built-ins do not exist there.
console.log silently does nothing — use this.getLogger().
fetch(), axios, fs, path, http, https, crypto do not exist in the sandbox.
All HTTP calls must use the IHttp accessor — http.get, http.post, http.del.
http.delete() does not exist — only http.del().

## Webhook request handling
Gemini calls JSON.parse(request.content) — RC already parsed it, double parsing corrupts data.
Gemini reads request.headers['Content-Type'] — RC lowercases all headers, always undefined.
Gemini hardcodes the endpoint URL — private endpoints have a server-generated hash that changes on reinstall.
Always surface the full URL dynamically using the privateHash from the request.

## Missing registrations that produce no error
Gemini sometimes registers features in the constructor or onEnable instead of extendConfiguration.
RC silently ignores registrations done outside extendConfiguration — no error, feature just never works.
Every slash command, endpoint, scheduler processor, and setting must be registered in extendConfiguration.
Endpoint and command classes need new MyClass(this) — passing the App instance is required.