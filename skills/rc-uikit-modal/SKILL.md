---
name: rc-uikit-modal
description: Use when user wants a form, popup, modal, dialog, input box, button, dropdown, or any interactive UI to collect input — like a feedback form, settings panel, confirmation dialog, or any screen that pops up in Rocket.Chat.
---

# RC UIKit Modal

## Interfaces to read (silently)
- `node_modules/@rocket.chat/apps-engine/definition/uikit/IUIKitSurface.d.ts`
- `node_modules/@rocket.chat/apps-engine/definition/uikit/IUIKitActionHandler.d.ts`
- `node_modules/@rocket.chat/apps-engine/definition/uikit/blocks/BlockBuilder.d.ts`
- `node_modules/@rocket.chat/apps-engine/definition/uikit/UIKitInteractionResponder.d.ts`

## RC specific rules — Gemini gets these wrong

**Trigger ID expires in seconds — open modal immediately**
Gemini fetches data first then opens the modal — trigger ID is already dead.
Always open the modal first, populate with data afterward if needed.

**BlockBuilder is deprecated — Gemini uses it anyway**
The .d.ts file marks BlockBuilder as deprecated with a warning.
Gemini uses it because it is in training data.
Use the surface-based approach with UIKitSurfaceType.MODAL instead.

**UIKit blocks come from apps-engine not a separate package**
Gemini imports from @rocket.chat/ui-kit — not installed by rc-apps create.
All block types live inside:
`node_modules/@rocket.chat/apps-engine/definition/uikit/blocks/`

**Always provide explicit blockId and actionId on inputs**
Gemini omits blockId and actionId — RC auto-generates UUIDs making values unreadable.
Always provide stable explicit IDs on every input element.

**Submitted values are nested two levels deep**
Gemini reads values directly by actionId — this fails silently.
RC nests them: view.state → blockId → actionId → value.

**notifyOnClose must be true for close handler to fire**
RC only fires executeViewClosedHandler if surface was created with notifyOnClose: true.
This property exists in IUIKitSurface — read it from the interface.

**No trigger ID in the close handler**
The close interaction has no trigger ID — modal opens from close handler fail silently.

**Block action handler must always return a response**
Gemini sometimes returns void — RC expects a response object always.