---
name: rc-app-settings
description: Use when user wants admin-configurable options, API keys, tokens, passwords, or any preference that should be changeable without touching code — like a bot name, API endpoint URL, or enable/disable toggles.
---

# RC App Settings

## Interfaces to read (silently)
- `node_modules/@rocket.chat/apps-engine/definition/settings/ISetting.d.ts`
- `node_modules/@rocket.chat/apps-engine/definition/settings/SettingType.d.ts`
- `node_modules/@rocket.chat/apps-engine/definition/accessors/ISettingRead.d.ts`

## RC specific rules — Gemini gets these wrong

**getValueById throws — getById returns undefined**
Gemini uses them interchangeably — they have different failure modes.
Confirmed in ISettingRead.d.ts — read it directly:
getById → returns undefined if setting does not exist, never throws
getValueById → throws an error if setting does not exist

**getValueById falls back to packageValue automatically**
Gemini adds manual fallback logic for default values — unnecessary.
RC already returns packageValue when no user-configured value exists.
packageValue field exists in ISetting.d.ts — it is the built-in default.

**Select and multiSelect types need the values array**
Gemini defines select settings without providing options — RC renders empty dropdown with no error.
ISetting.d.ts shows values field is optional — Gemini skips it because of that.
Always provide values array with key and i18nLabel for every SELECT and MULTI_SELECT setting.
ISettingSelectValue shape confirmed in ISetting.d.ts: { key: string, i18nLabel: string }

**PASSWORD type is not encrypted**
Gemini assumes PASSWORD type provides secure encrypted storage.
SettingType.d.ts shows PASSWORD is just another string type — only masks in admin UI.
Stored in plain text — never advertise it as secure storage.

**Settings survive app updates**
Gemini adds logic to preserve user config on update — unnecessary.
RC preserves existing user-configured values when provideSetting is called again.
Only new settings get their defaults — existing user values are never overwritten.

**i18n labels work as plain English strings**
Gemini creates translation key files for setting labels — unnecessary overhead.
RC displays the i18nLabel string as-is if no matching translation key exists.
Plain English works fine — no translation files needed unless multi-language is required.

**Guard required settings in onEnable**
Gemini lets the app enable without checking if required settings are configured.
Check critical settings in onEnable and return false to block activation.
onEnable receives IEnvironmentRead and IConfigurationModify — NOT IRead and IModify.
Gemini uses the wrong accessor types — read App.d.ts for the correct onEnable signature.