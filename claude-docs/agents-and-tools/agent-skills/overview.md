# Agent Skills Overview

## What are Agent Skills?

Agent Skills are organized folders of instructions, scripts, and resources that agents discover and load dynamically to perform better at specific tasks. They extend Claude's capabilities by providing specialized knowledge, standards, and workflows that Claude automatically applies when relevant.

A Skill is not just a prompt—it's a version-controlled, reusable asset that can contain both natural language guidance and deterministic, executable code.

## Key Concepts

### Progressive Disclosure

Skills use a three-tier progressive disclosure system:

1. **Discovery (Tier 1)**: At startup, Claude pre-loads only the **name** and **description** of each installed skill
2. **Activation (Tier 2)**: When your request matches a skill's description, Claude asks to use it and loads the full **SKILL.md** instructions
3. **Execution (Tier 3)**: As needed, Claude loads **additional resources** (reference docs, examples, scripts) referenced in SKILL.md

This approach keeps Claude's context efficient while maintaining access to specialized knowledge.

### Model-Invoked

Skills are **model-invoked**, meaning Claude decides which skills to use based on your request. You don't explicitly call skills—Claude automatically recognizes when a skill is relevant and applies it.

## Core Components

### SKILL.md File

Every skill requires a `SKILL.md` file with two parts:

#### 1. YAML Frontmatter (Required Metadata)

```yaml
---
name: skill-name
description: What this skill does and when to use it
---
```

**Required Fields:**
- **name**: Lowercase, alphanumeric with hyphens (max 64 chars)
- **description**: Clear explanation of capabilities and trigger conditions (max 1024 chars)

**Optional Fields:**
- **allowed-tools**: Restrict which tools Claude can use
- **model**: Specify a particular Claude model for this skill
- **context**: Set to `fork` to run in isolated sub-agent context
- **agent**: Agent type to use with `context: fork`
- **hooks**: Define lifecycle hooks (PreToolUse, PostToolUse, Stop)
- **user-invocable**: Control visibility in slash command menu (default: true)

#### 2. Markdown Instructions

The body contains step-by-step guidance, examples, and best practices that Claude follows when the skill is active.

### File Structure

**Single-file skill** (simple):
```
my-skill/
└── SKILL.md
```

**Multi-file skill** (complex):
```
my-skill/
├── SKILL.md (overview and navigation)
├── reference.md (detailed API docs)
├── examples.md (usage examples)
└── scripts/
    └── helper.py (utility script)
```

## How Skills Work

### 1. Discovery Phase

When Claude starts:
- Loads name + description of all installed skills
- Keeps these in memory for matching against user requests
- Does not load full instructions yet (saves tokens)

### 2. Matching Phase

When you make a request:
- Claude analyzes your message
- Compares against skill descriptions
- Identifies relevant skills

### 3. Activation Phase

When a skill matches:
- Claude asks to use the skill
- Loads full SKILL.md instructions
- Applies the guidance to your task

### 4. Execution Phase

During task execution:
- Claude follows skill instructions
- Loads additional resources as referenced
- May run scripts or use specified tools
- Returns results according to skill guidelines

## Skill Storage Locations

Skills can be stored in different locations for different scopes:

| Location | Path | Scope | Use Case |
|----------|------|-------|----------|
| **Personal** | `~/.claude/skills/` | You, across all projects | Your personal workflows |
| **Project** | `.claude/skills/` | Anyone in this repository | Team standards, project-specific tools |
| **Plugin** | Bundled with plugins | Anyone with plugin installed | Shared community skills |
| **Enterprise** | Managed settings | All organization users | Company-wide standards |

**Priority Order**: Managed > Personal > Project > Plugin

Same-named skills in higher-priority locations override lower-priority ones.

## Pre-built Skills

Anthropic provides ready-to-use skills for common tasks:

- **PowerPoint (pptx)**: Create and modify PowerPoint presentations
- **Excel (xlsx)**: Read, analyze, and manipulate spreadsheets
- **Word (docx)**: Generate and edit Word documents
- **PDF (pdf)**: Extract text, fill forms, merge documents

These are available immediately via:
- Claude API (with Skills API)
- Claude.ai (for paid users)
- Claude Code (via plugin marketplace)

## Creating Skills

### Basic Example

```yaml
---
name: generating-commit-messages
description: Generates clear commit messages from git diffs. Use when writing commit messages or reviewing staged changes.
---

# Generating Commit Messages

## Instructions

1. Run `git diff --staged` to see changes
2. I'll suggest a commit message with:
   - Summary under 50 characters
   - Detailed description
   - Affected components

## Best Practices

- Use present tense ("Add feature" not "Added feature")
- Explain what and why, not how
- Reference issue numbers when applicable
```

### Advanced Example with Resources

```yaml
---
name: api-documentation
description: Generate API documentation following company standards. Use when documenting APIs, endpoints, or REST services.
allowed-tools:
  - Read
  - Write
  - Grep
---

# API Documentation Generator

## Overview

This skill creates comprehensive API documentation following our standards.

## Instructions

1. Analyze the API code
2. Follow the template in [template.md](template.md)
3. Include examples from [examples.md](examples.md)
4. Validate using the checker script

## Usage

For reference, see:
- [API Standards](reference.md)
- [Example Docs](examples.md)

## Validation

Run validation:
```bash
python scripts/validate.py output.md
```
```

## Writing Effective Skill Descriptions

The **description** field is crucial—Claude uses it to decide when to activate your skill.

### Good Description Formula

Answer these questions:
1. **What does this skill do?** (List specific capabilities)
2. **When should Claude use it?** (Include trigger terms users would say)

### Examples

❌ **Bad**: "Helps with documents"
- Too vague
- No trigger terms
- Unclear capabilities

✅ **Good**: "Extract text and tables from PDF files, fill PDF forms, merge multiple PDFs into one document. Use when working with PDF files or when the user mentions PDFs, forms, document extraction, or merging documents."
- Lists specific capabilities
- Includes natural trigger phrases
- Clear about when to use

❌ **Bad**: "Database tool"
- No detail about what it does
- Missing trigger terms

✅ **Good**: "Query PostgreSQL databases using our company schema. Generates optimized SQL queries, explains query plans, and formats results as tables. Use when the user asks about database queries, customer data, reports, or mentions SQL, PostgreSQL, or database operations."
- Specific database type
- Clear capabilities
- Multiple trigger terms
- Natural language users might use

## Advanced Features

### Tool Restriction

Limit which tools Claude can use within a skill:

```yaml
---
name: read-only-analysis
description: Analyze code without making changes
allowed-tools: Read, Grep, Glob
---
```

Benefits:
- Prevent accidental modifications
- Enforce safe operations
- Clear about skill capabilities

### Isolated Context (Fork)

Run skills in separate sub-agent context:

```yaml
---
name: comprehensive-audit
description: Perform deep code analysis and generate detailed reports
context: fork
---
```

Benefits:
- Separate context prevents pollution
- Can use different model
- Ideal for intensive, isolated tasks

### Lifecycle Hooks

Add event-driven automation:

```yaml
---
name: secure-deploy
description: Deploy code with security checks
hooks:
  PreToolUse:
    - matcher: "Bash"
      hooks:
        - type: command
          command: "./scripts/security-check.sh $TOOL_INPUT"
          once: true
---
```

### Visibility Control

```yaml
user-invocable: false  # Hidden from menu, but Claude can still use it
```

Use for:
- Internal/helper skills
- Skills meant only for automatic activation
- Dependency skills for other skills

## Use Cases

### Code Quality & Standards
- Code review using team standards
- Commit message generation
- API documentation following conventions
- Test generation with project patterns

### Data & Analysis
- Database querying with company schemas
- Data validation and cleaning
- Report generation in specific formats
- Log analysis with domain knowledge

### Document Generation
- Technical specifications
- User documentation
- Compliance reports
- Branded presentations

### Workflow Automation
- Deployment procedures
- Incident response playbooks
- Onboarding checklists
- Release processes

### Domain Expertise
- Legal document analysis
- Medical terminology assistance
- Financial calculations
- Industry-specific workflows

## Skills vs. Other Options

Choose the right tool for your need:

| Feature | Triggered By | Best For | Persistent |
|---------|-------------|----------|------------|
| **Skills** | Claude (automatic) | Specialized knowledge, repeatable workflows | Yes |
| **Slash Commands** | You (`/command`) | Quick prompts, shortcuts | No |
| **CLAUDE.md** | Every conversation | Project-wide context, general instructions | Yes |
| **Subagents** | Explicit/Claude | Task delegation, separate context | Yes |
| **Hooks** | Specific events | Automation on events | Yes |
| **MCP Servers** | Tool calls | External tools/data sources | N/A |

**Use Skills when:**
- Task is specialized and repeatable
- You want Claude to automatically recognize when to use it
- Instructions are complex enough to warrant separate documentation
- You need version control and reusability

**Use CLAUDE.md when:**
- Instructions apply to all conversations in a project
- Context is general rather than task-specific
- Everyone working on the project needs the same baseline

## Best Practices

### 1. Start with Evaluation
- Identify specific gaps in Claude's capabilities
- Run Claude on representative tasks
- Observe where it struggles or needs more context
- Create skills to fill those gaps

### 2. Write Clear Descriptions
- Include keywords users naturally say
- List specific capabilities
- Be specific about trigger conditions
- Test with various phrasings

### 3. Keep SKILL.md Focused
- Under 500 lines for main file
- Split detailed content into separate files
- Use progressive disclosure
- Reference external resources

### 4. Provide Examples
- Show typical usage patterns
- Include edge cases
- Demonstrate expected output
- Add troubleshooting examples

### 5. Test Thoroughly
- Try various phrasings to trigger the skill
- Test with and without explicit skill invocation
- Verify tool restrictions work
- Check resource loading

### 6. Version Control
- Store skills in git
- Document changes
- Tag releases
- Share across team

### 7. Iterate Based on Usage
- Monitor which skills are used
- Refine descriptions based on activation accuracy
- Update instructions based on Claude's interpretation
- Add examples for common mistakes

## Troubleshooting

### Skill Not Triggering

**Problem**: Claude doesn't recognize when to use your skill

**Solutions**:
- Add more trigger keywords to description
- Include natural phrases users would say
- Be more specific about when to use it
- Test description with various queries

### Skill Doesn't Load

**Problem**: Skill file not found or not loaded

**Check**:
- File path: `~/.claude/skills/skill-name/SKILL.md` (personal) or `.claude/skills/skill-name/SKILL.md` (project)
- YAML syntax: starts with `---` on line 1, no blank lines before
- Indentation: use spaces, not tabs
- File permissions: readable by Claude

### Skill Has Errors

**Problem**: Skill loads but fails during execution

**Check**:
- Dependencies are installed
- Script permissions: `chmod +x scripts/*.py`
- Path separators: use `/` not `\`
- Tool restrictions aren't blocking needed operations
- Referenced files exist and are accessible

### Wrong Skill Activates

**Problem**: Claude uses a different skill than expected

**Solutions**:
- Make descriptions more specific
- Add exclusion terms ("Do not use for...")
- Check for overlapping descriptions
- Adjust priority by moving to different location

## API Integration

Skills can be used via the Claude API:

### Skills API Quickstart

1. **Upload Skills**: Use the Skills API to create and manage custom skills
2. **Reference in Requests**: Skills are automatically available to Claude
3. **Monitor Usage**: Track which skills are invoked
4. **Update as Needed**: Modify skill definitions dynamically

### Pre-built Skills via API

Use Anthropic's pre-built skills:
- PowerPoint, Excel, Word, PDF skills available immediately
- No custom skill creation needed
- Production-ready implementations

## Open Standard

The Agent Skills format is an **open standard** originally developed by Anthropic:

- **Specification**: Available at [agentskills.io](https://agentskills.io)
- **Adoption**: Used by OpenAI, GitHub, Microsoft, Cursor, and others
- **Community**: Growing ecosystem of shared skills
- **Contributions**: Open to contributions from the broader ecosystem

### Cross-Platform Compatibility

Skills written for Claude Code can potentially work with:
- Other AI coding assistants
- Different agent platforms
- Custom agent implementations

This standardization creates a marketplace of reusable agent capabilities.

## Resources

- **Official Repository**: [github.com/anthropics/skills](https://github.com/anthropics/skills) - Example skills and templates
- **Specification**: [agentskills.io](https://agentskills.io) - Complete standard specification
- **API Docs**: Skills API reference for programmatic access
- **Cookbook**: Agent Skills Cookbook for creating custom skills
- **Community**: Marketplaces and repositories of shared skills

## Platform Availability

Agent Skills are available on:
- **Claude Code**: Via plugin marketplace and local installation
- **Claude.ai**: For paid users
- **Claude API**: Via Skills API for custom integration
- **Agent SDK**: For building custom agents with skills

## Next Steps

1. **Explore Pre-built Skills**: Try PowerPoint, Excel, Word, and PDF skills
2. **Read the Quickstart**: Learn to create your first custom skill
3. **Review Examples**: Study skills in the official repository
4. **Create Your First Skill**: Start with something simple for your workflow
5. **Share**: Contribute to the community skills marketplace

---

*Last updated: January 2026*
*Specification: [agentskills.io](https://agentskills.io)*
*Official repository: [github.com/anthropics/skills](https://github.com/anthropics/skills)*
