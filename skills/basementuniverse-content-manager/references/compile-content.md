# compile-content.ts Utility

## Purpose

compile-content.ts is a CLI helper that pre-inlines text/json data into a content list JSON file.

## Invocation

```bash
ts-node compile-content.ts -i content.json -o content-compiled.json
```

Options:
- -i, --input: path to source content list JSON
- -o, --output: path to write compiled JSON

## What It Does

For each item in the input list:
- Only considers items where type is text or json.
- Looks at args[0].
- If args[0] is a string starting with ./ or http://:
  - Reads content from local file (./) or fetches over HTTP (http://).
  - Replaces args[0] with inlined raw text (text type) or parsed object (json type).

## Important Limitations

- It does not look for url: prefix used by runtime JSONLoader/TextLoader.
- It checks only ./ and http:// prefixes (not https://).
- It processes only args[0], not additional args.
- Input validation/error messaging is minimal.

## Typical Workflow

1. Author content list with external text/json references.
2. Run compile-content.ts in CI/build step.
3. Ship compiled JSON with text/json payloads inlined.
