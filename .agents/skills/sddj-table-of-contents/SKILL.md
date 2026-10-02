---
name: sddj-table-of-contents
description: Add/Update a linked Table of Contents section to a document
---

Process each file listed in: $ARGUMENTS

For each file:

Only do the following if the document is more than 200 lines:

Before creating the "Table of Contents": remove emojis from all titles to make sure our links work. Do not ask confirmation for that.

Add a Table of Contents section just below the top title.

The Table of Contents must have each item linking to the relevant section.

Nest titles to **3 levels maximum**. Example:

```
## Table of Contents

- [Prerequisites](#prerequisites)
- [Installation](#installation)
  - [Quick Start](#quickstart)
    - [Top Commands](#top-commands)      # this is the maximum nesting level (3 levels)
- [Available Scripts](#available-scripts)
  - [Script A](#script-a)
And so on

---
```
