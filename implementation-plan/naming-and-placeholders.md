# Implementation Plan Template Naming And Placeholder Guide

Use this helper when turning the implementation-plan templates into real feature documents.

## File Naming

- Outline file: `<feature-slug>-implementation-plan-outline.md`
- Stage file: `<feature-slug>-implementation-plan-stage-<n>.md`
- Keep `<feature-slug>` stable across the outline and every stage file.
- Use lowercase kebab-case for `<feature-slug>`.
- Number stages sequentially starting at `1`.

## Title Conventions

- Outline title: `# <Feature Name> Implementation Plan Outline`
- Stage title: `# Stage <N> Implementation Plan: <Stage Name>`
- Use title case for `<Feature Name>`.
- Keep `<Stage Name>` short, specific, and responsibility-based.

## Placeholder Rules

- `<feature name>`: human-readable feature name.
- `<feature-slug>`: lowercase kebab-case identifier used in filenames and Markdown links.
- `<module path>`: repo-relative path to the affected module or service.
- `<module alias>`: optional short name used by the team for that module.
- `<module_name>`: existing module or service name used in style guidance.
- `<authoritative spec / ticket / ADR>`: highest-priority source of truth.
- `<N>`: numeric stage number matching the outline order.

Replace all placeholders before using the document as an actual implementation plan.

## Linking Rules

- In the outline, link each stage with a relative Markdown link to the matching stage file.
- Keep filenames and links synchronized if a stage is renamed or renumbered.
- Stage files inherit the `Implementation Working Agreement` from the corresponding outline and
  should not duplicate it unless they need stricter stage-specific rules.
- Before final use, verify every Markdown link resolves to the intended local file or an explicitly
  external source of truth.

## Path And Command Validation

- Verify every referenced repository path exists, unless the plan explicitly says the file or
  directory will be created by that stage.
- Verify command working directories match the nearest relevant module or service root.
- Include mandatory repository flags and toolchain setup in command examples.
- If a path, command, or generated-code location is uncertain, record it as an open question instead
  of guessing.

## Cleanup Before Final Use

- Remove template instruction callouts that are no longer useful.
- Delete unused placeholder bullets and example text.
- Keep only the sections that help implement and verify the real feature.
- Confirm there are no remaining `<placeholder>` values.
- Confirm every stage listed in the outline has a matching stage file, and every stage file is linked
  from the outline.
