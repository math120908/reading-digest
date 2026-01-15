# Agent Skills Quickstart

This quickstart guide will help you create your first Agent Skill and understand how to use pre-built skills.

## Quick Start: Using Pre-built Skills

Anthropic provides ready-to-use skills for common document tasks. These are the fastest way to get started.

### Available Pre-built Skills

| Skill | What It Does | Use When |
|-------|-------------|----------|
| **PowerPoint (pptx)** | Create and modify presentations | Building slide decks, reports |
| **Excel (xlsx)** | Read and manipulate spreadsheets | Data analysis, reports |
| **Word (docx)** | Generate and edit documents | Writing docs, proposals |
| **PDF (pdf)** | Extract text, fill forms, merge | Working with PDFs |

### Using Pre-built Skills

#### Via Claude Code
```bash
# Install the official skills plugin
/plugin install anthropic-agent-skills

# Skills are now available automatically
# Just ask Claude naturally:
"Create a PowerPoint presentation about Q4 results"
```

#### Via Claude.ai
Pre-built skills are automatically available for paid Claude.ai users. Simply request:
- "Create an Excel spreadsheet with this data..."
- "Generate a Word document for this proposal..."
- "Extract text from this PDF..."

#### Via Claude API
See the Skills API documentation for programmatic access to pre-built skills.

## Creating Your First Custom Skill

Let's create a simple skill that helps write commit messages following your team's standards.

### Step 1: Choose a Location

Personal skill (just for you):
```bash
mkdir -p ~/.claude/skills/commit-message-generator
```

Project skill (for your team):
```bash
mkdir -p .claude/skills/commit-message-generator
```

### Step 2: Create SKILL.md

Create `~/.claude/skills/commit-message-generator/SKILL.md`:

```yaml
---
name: commit-message-generator
description: Generates clear, conventional commit messages from git diffs. Use when writing commit messages, committing changes, or reviewing staged changes.
---

# Commit Message Generator

## Purpose
Generate commit messages following Conventional Commits format with clear, concise descriptions.

## Instructions

When the user wants to commit changes:

1. **Analyze changes**: Run `git diff --staged` to see what's being committed

2. **Categorize the change**:
   - `feat`: New feature
   - `fix`: Bug fix
   - `docs`: Documentation only
   - `style`: Formatting, missing semicolons, etc.
   - `refactor`: Code change that neither fixes a bug nor adds a feature
   - `test`: Adding or updating tests
   - `chore`: Build process, dependencies, etc.

3. **Generate message** in this format:
   ```
   <type>(<scope>): <short description>

   <detailed description if needed>

   <footer with issue references if applicable>
   ```

4. **Keep the subject line under 50 characters**

5. **Use present tense**: "Add feature" not "Added feature"

## Examples

### Good commit messages:
```
feat(auth): add OAuth2 authentication

Implement OAuth2 flow with Google and GitHub providers.
Includes token refresh and session management.

Closes #123
```

```
fix(api): handle null response in user endpoint

Added null check to prevent crash when user not found.
```

```
docs(readme): update installation instructions
```

### What to avoid:
- Vague messages: "Update stuff"
- Too long subject lines
- Past tense: "Added feature"
- Missing type prefix

## Best Practices

- Reference issue numbers in footer
- Explain WHY, not just WHAT
- Keep subject line informative but concise
- Add details in body for complex changes
```

### Step 3: Test Your Skill

Skills are automatically loaded when created. Test it:

```bash
# Stage some changes
git add .

# Ask Claude
"I need to commit these changes"
```

Claude will:
1. Recognize the skill is relevant
2. Ask to use it
3. Analyze your staged changes
4. Generate a proper commit message

## Creating a More Complex Skill

Let's build a skill with multiple resources and scripts.

### Step 1: Create Directory Structure

```bash
mkdir -p ~/.claude/skills/code-reviewer/{scripts,references,examples}
cd ~/.claude/skills/code-reviewer
```

### Step 2: Create Main SKILL.md

`SKILL.md`:
```yaml
---
name: code-reviewer
description: Review code for quality, security, and best practices. Use when reviewing pull requests, code changes, or conducting code audits.
allowed-tools:
  - Read
  - Grep
  - Glob
  - Bash
---

# Code Reviewer

## Purpose
Perform comprehensive code reviews following team standards.

## Instructions

1. **Understand the change**:
   - Ask what was changed and why
   - Review the diff or files provided

2. **Run automated checks**:
   ```bash
   python scripts/lint-check.py
   ```

3. **Review for**:
   - Code quality (see [quality-checklist.md](references/quality-checklist.md))
   - Security issues (see [security-patterns.md](references/security-patterns.md))
   - Performance implications
   - Test coverage

4. **Provide feedback** in this format:
   - **Critical**: Must fix before merge
   - **Suggestion**: Should consider
   - **Nitpick**: Nice to have

5. **Reference examples** from [good-reviews.md](examples/good-reviews.md)

## Output Format

Provide review as:
```markdown
## Summary
[Overall assessment]

## Critical Issues
- [ ] [Issue with line number and explanation]

## Suggestions
- [Suggestion with rationale]

## Positive Notes
- [Things done well]
```

## Checklist
- [ ] Code follows team style guide
- [ ] Tests added/updated
- [ ] Documentation updated
- [ ] No security vulnerabilities
- [ ] Error handling is robust
- [ ] Performance is acceptable
```

### Step 3: Add Supporting Files

`references/quality-checklist.md`:
```markdown
# Code Quality Checklist

## Readability
- [ ] Variables have descriptive names
- [ ] Functions are single-purpose
- [ ] Comments explain WHY, not WHAT
- [ ] Complex logic is documented

## Maintainability
- [ ] DRY: No unnecessary repetition
- [ ] Functions are < 50 lines
- [ ] Classes have clear responsibilities
- [ ] Dependencies are minimal

## Robustness
- [ ] Input validation
- [ ] Error handling
- [ ] Edge cases covered
- [ ] Null/undefined checks
```

`references/security-patterns.md`:
```markdown
# Security Patterns

## Input Validation
- Always validate user input
- Sanitize before database queries
- Use parameterized queries

## Authentication
- Never store passwords in plain text
- Use established libraries (bcrypt, etc.)
- Implement rate limiting

## Common Vulnerabilities
- SQL injection
- XSS attacks
- CSRF tokens
- Insecure deserialization
```

`examples/good-reviews.md`:
```markdown
# Example Code Reviews

## Example 1: Feature Addition

### Summary
Great addition of the user authentication feature! Code is clean and well-tested.

### Critical Issues
- [ ] Line 45: Password is logged in error message. Remove sensitive data from logs.
- [ ] Line 78: SQL query is vulnerable to injection. Use parameterized query.

### Suggestions
- Consider adding rate limiting to login endpoint
- Could extract validation logic into separate function for reusability

### Positive Notes
- Excellent test coverage (95%)
- Clear error messages for users
- Good documentation
```

`scripts/lint-check.py`:
```python
#!/usr/bin/env python3
"""
Simple linting check for common issues
"""
import sys
import os

def check_file(filepath):
    issues = []
    with open(filepath, 'r') as f:
        lines = f.readlines()

    for i, line in enumerate(lines, 1):
        # Check for common issues
        if 'TODO' in line:
            issues.append(f"Line {i}: TODO comment found")
        if 'console.log' in line or 'print(' in line:
            issues.append(f"Line {i}: Debug statement left in code")
        if len(line) > 120:
            issues.append(f"Line {i}: Line too long ({len(line)} chars)")

    return issues

if __name__ == '__main__':
    if len(sys.argv) < 2:
        print("Usage: lint-check.py <file>")
        sys.exit(1)

    issues = check_file(sys.argv[1])
    if issues:
        print("Issues found:")
        for issue in issues:
            print(f"  - {issue}")
        sys.exit(1)
    else:
        print("No issues found!")
        sys.exit(0)
```

Make the script executable:
```bash
chmod +x scripts/lint-check.py
```

### Step 4: Test the Complex Skill

```bash
# Ask Claude to review code
"Please review the changes in src/auth.js"
```

Claude will:
1. Activate the code-reviewer skill
2. Read the file
3. Run the lint check script
4. Apply the quality and security checklists
5. Provide structured feedback

## Writing Effective Skill Descriptions

The description is crucial for skill activation. Here's how to write them:

### Formula

```
[What it does] + [When to use it] + [Trigger keywords]
```

### Examples

❌ **Too vague**:
```yaml
description: Helps with git
```

✅ **Clear and specific**:
```yaml
description: Generates conventional commit messages from git diffs, following team standards. Use when writing commits, committing changes, or when the user mentions git commit, commit message, or staged changes.
```

❌ **Missing trigger keywords**:
```yaml
description: Reviews Python code for quality issues
```

✅ **Includes natural phrases**:
```yaml
description: Reviews Python code for quality, security, and PEP 8 compliance. Checks for common bugs and performance issues. Use when reviewing code, pull requests, or when the user asks to check, review, audit, or analyze Python code.
```

## Common Patterns

### Pattern 1: Analysis Skills

```yaml
---
name: log-analyzer
description: Analyze application logs to identify errors, patterns, and anomalies. Use when debugging, investigating issues, or when the user mentions logs, errors, debugging, or troubleshooting.
allowed-tools:
  - Read
  - Grep
  - Bash
---

# Log Analyzer

## Instructions
1. Ask for log file location
2. Search for error patterns
3. Group similar errors
4. Identify timestamps and frequency
5. Suggest likely causes

## Error Patterns
- Exception stack traces
- HTTP 5xx errors
- Timeout messages
- Database connection failures
```

### Pattern 2: Generation Skills

```yaml
---
name: api-doc-generator
description: Generate OpenAPI/Swagger documentation from API code. Use when documenting APIs, endpoints, or when the user mentions API docs, swagger, OpenAPI, or endpoint documentation.
allowed-tools:
  - Read
  - Write
  - Grep
---

# API Documentation Generator

## Instructions
1. Scan API routes/controllers
2. Extract endpoints, methods, parameters
3. Generate OpenAPI 3.0 spec
4. Include examples and descriptions
5. Validate generated spec
```

### Pattern 3: Workflow Skills

```yaml
---
name: release-checklist
description: Guide through release process with company-specific checklist. Use when preparing releases, deploying, or when the user mentions release, deploy, or launch.
---

# Release Checklist

## Pre-release
- [ ] All tests pass
- [ ] Version number updated
- [ ] CHANGELOG updated
- [ ] Documentation updated

## Release
- [ ] Create git tag
- [ ] Build production artifacts
- [ ] Deploy to staging
- [ ] Smoke test staging

## Post-release
- [ ] Deploy to production
- [ ] Monitor for errors
- [ ] Notify stakeholders
- [ ] Update project board
```

## Advanced Features

### Tool Restrictions

Limit tools for safety:

```yaml
---
name: read-only-audit
description: Audit code without making any changes
allowed-tools:
  - Read
  - Grep
  - Glob
---
```

### Isolated Context

Run in separate context for intensive tasks:

```yaml
---
name: comprehensive-analysis
description: Deep code analysis with detailed reporting
context: fork
agent: subagent
---
```

### Lifecycle Hooks

Add automation:

```yaml
---
name: secure-deploy
description: Deploy with security checks
hooks:
  PreToolUse:
    - matcher: "Bash"
      hooks:
        - type: command
          command: "./scripts/security-check.sh $TOOL_INPUT"
          once: true
---
```

### Hidden Skills

Create helper skills:

```yaml
---
name: internal-helper
description: Internal utility for other skills
user-invocable: false
---
```

## Troubleshooting

### Problem: Skill doesn't activate

**Symptoms**: Claude doesn't recognize when to use your skill

**Solutions**:
1. Add more trigger keywords to description:
   ```yaml
   description: ... Use when the user asks about X, mentions Y, or says Z.
   ```

2. Be more specific about capabilities:
   ```yaml
   description: This skill does A, B, and C. [previous description]
   ```

3. Test with different phrasings:
   - "I need to commit my changes"
   - "Write a commit message"
   - "Help me commit this"

### Problem: Skill file not found

**Symptoms**: Error that skill doesn't exist

**Solutions**:
1. Check path:
   ```bash
   # Personal
   ~/.claude/skills/skill-name/SKILL.md

   # Project
   .claude/skills/skill-name/SKILL.md
   ```

2. Verify YAML frontmatter:
   - Must start on line 1 with `---`
   - No blank lines before it
   - Properly closed with `---`

3. Check file name: Must be exactly `SKILL.md` (case-sensitive)

### Problem: Script errors

**Symptoms**: Skill activates but scripts fail

**Solutions**:
1. Make scripts executable:
   ```bash
   chmod +x scripts/*.py
   chmod +x scripts/*.sh
   ```

2. Use correct paths:
   ```python
   # Use forward slashes on all platforms
   scripts/helper.py  # ✓
   scripts\helper.py  # ✗
   ```

3. Check dependencies:
   ```bash
   # Add requirements to skill directory
   pip install -r requirements.txt
   ```

### Problem: Wrong skill activates

**Symptoms**: Different skill than expected runs

**Solutions**:
1. Make descriptions more distinct
2. Check for overlapping trigger keywords
3. Use exclusions:
   ```yaml
   description: ... Do not use for unit testing or integration testing.
   ```

## Best Practices Checklist

- [ ] **Description is specific** (includes what, when, and keywords)
- [ ] **Name is descriptive** and follows naming convention
- [ ] **Instructions are clear** with step-by-step guidance
- [ ] **Examples are included** showing expected output
- [ ] **Resources are split** (SKILL.md < 500 lines)
- [ ] **Scripts are executable** (`chmod +x`)
- [ ] **Tools are restricted** if appropriate
- [ ] **Tested with variations** of trigger phrases
- [ ] **Version controlled** in git
- [ ] **Documented** with comments and README

## Next Steps

### 1. Start Simple
Create a basic single-file skill for a common task you do

### 2. Test Thoroughly
Try different ways of asking Claude to trigger it

### 3. Iterate
Refine based on how Claude interprets your instructions

### 4. Add Complexity
Split into multiple files, add scripts, use advanced features

### 5. Share
Contribute useful skills to the community

## Resources

- **Example Skills**: [github.com/anthropics/skills](https://github.com/anthropics/skills)
- **Full Specification**: [agentskills.io/specification](https://agentskills.io/specification)
- **Skills API**: See API documentation for programmatic access
- **Agent Skills Cookbook**: Detailed guide for creating custom skills
- **Community Marketplace**: Browse and share skills

## Common Skill Templates

### Code Quality Skill Template

```yaml
---
name: [language]-code-reviewer
description: Review [language] code for quality, style, and best practices. Use when reviewing code, PRs, or auditing [language] files.
allowed-tools:
  - Read
  - Grep
  - Bash
---

# [Language] Code Reviewer

## Instructions
1. Read the code files
2. Check against style guide
3. Look for common anti-patterns
4. Verify error handling
5. Check test coverage

## Checklist
- [ ] Style guide followed
- [ ] Error handling robust
- [ ] Tests included
- [ ] Documentation clear

## Common Issues
[List common issues in this language]
```

### Documentation Generator Template

```yaml
---
name: [type]-doc-generator
description: Generate [type] documentation from code. Use when creating docs, documenting APIs, or generating [format] documentation.
allowed-tools:
  - Read
  - Write
  - Grep
---

# [Type] Documentation Generator

## Instructions
1. Scan source files
2. Extract key information
3. Generate [format] documentation
4. Add examples
5. Validate output

## Template
[Your documentation template here]
```

### Workflow Automation Template

```yaml
---
name: [workflow]-automation
description: Automate [workflow] process following company standards. Use when performing [workflow] or when user mentions [keywords].
---

# [Workflow] Automation

## Steps
1. [Step 1]
2. [Step 2]
3. [Step 3]

## Validation
- [ ] Checklist item 1
- [ ] Checklist item 2

## Rollback Procedure
[How to undo if needed]
```

---

*Last updated: January 2026*
*Get started: Create your first skill in `~/.claude/skills/`*
*Learn more: [agentskills.io](https://agentskills.io)*
