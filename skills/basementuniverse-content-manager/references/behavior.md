# Behavioral Notes And Gotchas

## Lifecycle And State

- The library is a singleton; initialise() must be called once.
- load() updates status as loading -> processing -> ready.
- progress includes both load tasks and processor tasks.
- Existing content dictionary persists across multiple load() calls.

## Loading Semantics

- Items are loaded sequentially in list order.
- Existing item names are overwritten with new loaded content.
- For json loader:
  - If first arg is a string starting with url:, it fetches JSON.
  - Otherwise it returns arg as-is.
- For text loader:
  - If first arg starts with url:, it fetches text.
  - Otherwise it returns arg.toString().
- image/audio/font loaders expect URL-like args and use browser element APIs.

## Processing Semantics

- Processors run only after all items are loaded.
- Processors execute sequentially per item, in declaration order.
- Processor functions can mutate both the current item and the full content map.
- Missing processor registrations throw during load().

## Error Behavior

- initialise() twice throws.
- dispose() before initialise() throws.
- get()/load()/status/progress before initialise() throws.
- Missing loader for a type throws.
- If throwOnNotFound is true, get() throws for unknown name.

## Browser Runtime Expectations

- Uses window.fetch for network-backed json/text loading.
- Uses Image, Audio, FontFace, and document.fonts.
- Intended for browser-capable runtimes.
