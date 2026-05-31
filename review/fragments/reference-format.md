# Reference Format

## Path formatting rules

- Use repo-root-relative paths everywhere in the report.
- Never use relative navigation such as `../` or `../../`.
- Normalize any copied notes or references into repo-root-relative form before writing the report.
- Keep the final three report blocks in this exact order at the very end of the document: `Spec sections checked`, `Additional context checked`, `Files inspected`.
- Write `Spec sections checked`, `Additional context checked`, and `Files inspected` as separate collapsible `<details>` blocks.
- In `Spec sections checked`, use a nested list: write the specification directory path once, list each spec filename once, and put each referenced section or paragraph on its own child bullet beneath that file. If a referenced section has child items or criteria, nest those on their own bullets too.
- In `Files inspected`, group entries by shared repo-root-relative parent directory. Write each parent directory once, then list only filenames beneath it.
- At the bottom of each finding, write `Spec references`, `Additional context references`, and `File:line` as separate collapsible `<details>` blocks.
- For `Spec references`, `Additional context references`, and any similar grouped reference blocks, use nested lists by file. Do not inline multiple section, paragraph, or criteria references on one line separated by commas or semicolons. If a referenced section has child items or criteria, nest those on their own bullets too.
- Keep `File:line` entries repo-root relative and format them as a list inside the collapsible block.
