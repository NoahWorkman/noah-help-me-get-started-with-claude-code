---
name: review
description: Review current changes for quality, bugs, and style issues
---

Review all staged and unstaged changes in this repository. For each file changed:

1. Check for bugs, logic errors, and edge cases
2. Check for security issues (injection, XSS, exposed secrets)
3. Check for style consistency with the rest of the codebase
4. Check for missing error handling
5. Check for missing tests

Format your review as:

**filename.ts**
- [BUG] Description of issue (line X)
- [STYLE] Description of issue (line X)
- [SECURITY] Description of issue (line X)

If everything looks good, say so. Don't invent issues that aren't there.

End with a summary: ship it, needs minor fixes, or needs major rework.
