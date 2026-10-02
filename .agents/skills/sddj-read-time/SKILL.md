---
name: sddj-read-time
description: Add/Update reading time to a document
---

**Argument validation:**

- If `$ARGUMENTS` is empty, report "No files or directories provided." and prompt the user to provide paths before proceeding.
- For each argument, verify the path exists. If it does not, report it as invalid and skip it. If all paths are invalid, stop.

Process each valid argument in: $ARGUMENTS

For each argument:

- If it is a directory:
  - List only `.md` and `.txt` files in the top level, show the full list to the user, and ask for confirmation before proceeding.
  - Also ask: "Should I recurse into subdirectories?" — only recurse if the user confirms.
- If it is a file, process it directly.

For each file, add/update reading time between the YAML frontmatter (the closing `---`) and the top-level title (the first `#` heading), such as: "Xmin read, for Y words and Z lines"

The target structure is:

```
---
(frontmatter)
---

Xmin read, for Y words and Z lines

# Title
```

If there is no `#` title, place reading time just below the YAML frontmatter (or at the very top if there is no frontmatter either).

**Idempotency:** Before inserting, scan only the zone between the closing `---` of the frontmatter and the first `#` heading (or the top of the file if no frontmatter exists). If a line matching `*min read*` exists within that zone, replace it in place. Do not scan the rest of the file body — matches there are irrelevant.

**Calculating read time and counts:**

- Run: `wc -w [filepath]`
- Also run: `wc -l [filepath]`
- Divide word count by 200 (words per minute)
- Round **up** to the nearest whole number (`ceil`)
- If result is 0 or less than 1, display `< 1 min read` instead
- Keep the raw word count and line count in the output string

Format the inserted line exactly like this:

`Xmin read, for Y words and Z lines`

**Summary:** After all files are processed, output a table listing each file, whether it was added/updated, and the full read-time string assigned.
