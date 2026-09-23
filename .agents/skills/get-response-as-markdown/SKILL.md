---
name: get-response-as-markdown
description: Format and copy last response (prettify tables, spacing) to clipboard
---

1. Recall the last meaningful content block from your previous response (table, code block, list, etc.)
2. Format it with proper markdown alignment (aligned table columns, clean spacing)
3. Copy directly to clipboard using a heredoc:

```bash
cat <<'EOF' | pbcopy
<formatted content here>
EOF
```

Confirm to the user what was copied.
