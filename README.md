# basementuniverse/agent-skills

This repository is a central catalogue of skills for effectively using basementuniverse game components and other libraries in your projects.

## Overview

Within the `/skills` directory, we have 2 collections:

1. `/skills/packages` - a collection of skills for using basementuniverse packages and libraries. Don't modify these directly! They are maintained in their respective package repositories and automatically deployed to this catalogue via CI scripts.

2. `/skills/misc` - any other skills that are not tied to a specific package. These can be authored and maintained directly in this repository.

## Discovering and provisioning skills

Skills in this catalogue can be searched using:

```bash
gh skill search "search term or package name" --owner basementuniverse
```

Skills in this catalogue can be installed into your project using:

```bash
gh skill install "skill name" --owner basementuniverse
```

See [gh skill](https://cli.github.com/manual/gh_skill) for more information.

## Authoring a skill

### Overview

1. Go to the package repository...
1. Create the directory structure and author the skill
2. Set up a CI script to deploy the skill to this catalogue

### Directory structure

In the package repository, set up the skills directory like so:

```txt
skills/
  {skill name}/
    SKILL.md
    references/
      *.md
    scripts/
      *.mjs
src/
  ...
README.md
package.json
```

A project can have multiple skills. Typically we will have a default "how to use this library" skill named after the library itself (e.g. `basementuniverse-animation`).

References and scripts are optional.

### Skill contents

The `*.md` files should be well-structured Markdown files. The main `SKILL.md` file should have some YAML frontmatter at the top, for example:

```md
---
name: basementuniverse-animation
description: >
  Use and troubleshoot the @basementuniverse/animation TypeScript library
  for value animation in HTML5 games. Use when implementing, debugging, or
  documenting animation behaviour driven by this package.
---
```

The description field should state what the skill is for and when it should be used. The name field should match the skill directory name.

If a skill has references, then the main `SKILL.md` file should also contain a references section, for example:

```md
## References

- Public API surface: [references/api.md](references/api.md)
```

### The CI script

Still within the package repository, create a `/.github/workflows` directory and copy in the `/ci/agent-skills-package.yml` file from this repository.

Update the scope to match the package name.

Create a Github secret called `AGENT_SKILLS_TOKEN` and paste in the token.

## Deploying a skill to the catalogue

The CI script is set up to open a PR in this central catalogue repository, adding the package's skill contents to `/skills/packages/{package name}/...`.

This CI pipeline will run when pushing changes to the `main` branch with a tag containing the semver and a `-skills` suffix. The PR must be approved and merged (in *this* repository) before the skill is available in the catalogue.

When all of the above has been done, commit and push the changes with a tag like so:

```bash
git tag v1.0.0-skills
git push origin main --tags
```
