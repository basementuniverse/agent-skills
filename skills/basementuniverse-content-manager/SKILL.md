---
name: basementuniverse-content-manager
description: >
  Use this skill when implementing, debugging, or extending content loading with
  @basementuniverse/content-manager in browser-based TypeScript/JavaScript
  projects, including custom loaders/processors and content pipeline setup.
---

# Basement Universe Content Manager

Use this skill when working with @basementuniverse/content-manager.

## When To Use

Use this skill for tasks like:
- Setting up content loading for images, audio, fonts, JSON, or text.
- Designing or debugging content manifests passed to ContentManager.load().
- Implementing custom loaders for new content types.
- Implementing post-load processors for data transformation.
- Diagnosing load/progress/status behavior and missing-content errors.
- Using the compile-content.ts utility to pre-inline JSON/text content.

## When Not To Use

Do not use this skill when:
- You need non-browser asset loading (this library depends on browser APIs like window.fetch, Image, Audio, FontFace, and document.fonts).
- You need advanced streaming/caching/CDN orchestration beyond this library's in-memory load/transform model.

## Quick Start Pattern

1. Initialise once, optionally extending loaders/processors.
2. Load a content list with name/type/args (+ optional processors).
3. Read ContentManager.progress and ContentManager.status during loading.
4. Retrieve assets using ContentManager.get<T>(name).

Example:

```ts
import ContentManager from '@basementuniverse/content-manager';

ContentManager.initialise({
  throwOnNotFound: true,
});

await ContentManager.load([
  { name: 'hero', type: 'image', args: ['/assets/hero.png'] },
  { name: 'strings', type: 'json', args: ['url:/assets/strings.json'] },
]);

const hero = ContentManager.get<HTMLImageElement>('hero');
```

## Practical Guidance

- Call ContentManager.initialise() exactly once before load/get.
- Use url: prefix only for json/text when you want fetch-based loading.
- For image/audio/font loaders, pass direct URL strings as args[0].
- Processors execute after all load steps complete, then per item processor list.
- Re-loading with the same name replaces that content item's content.
- Use throwOnNotFound: true in stricter runtime environments.

## Common Pitfalls

- Calling initialise() twice throws an error.
- Calling load()/get()/status/progress before initialise() throws.
- Missing loader type or processor name in config throws.
- text loader assumes args[0] supports startsWith when using URL mode.
- compile-content.ts inlines text/json only for args[0] that start with ./ or http://.

## References

- Public API surface: [references/api.md](references/api.md)
- Types and contracts: [references/types.md](references/types.md)
- Behavioral details and gotchas: [references/behavior.md](references/behavior.md)
- Utility script reference: [references/compile-content.md](references/compile-content.md)
