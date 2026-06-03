# LLM Wiki Schema & Operations Guide

This document defines the structure, conventions, and operational workflows for this knowledge base. Any LLM agent operating in this repository MUST follow these rules to ensure the wiki remains consistent, clean, and highly maintainable.

---

## 1. Directory Layout

The workspace is organized into three main layers:

```
second-brain/
├── raw/                 # Immutable raw source documents
│   └── assets/          # Downloaded attachments and images for sources
├── wiki/                # LLM-maintained markdown knowledge base
│   ├── index.md         # Content index and categorization
│   └── log.md           # Append-only chronological log of actions
└── SCHEMA.md            # This configuration and rules guide (read-only for LLM)
```

---

## 2. Document Conventions

To ensure visual appeal in Obsidian and ease of programmatic manipulation, all wiki documents must follow these standards.

### 2.1 File Naming
- Use **Title Case** with spaces for filenames (e.g., `Tolkien Gateway.md`, `Obsidian Web Clipper.md`).
- Prefix system files or specific page types for clear grouping:
  - `Summary - [Source Title].md` for raw document summaries.
  - `Synthesis - [Topic Name].md` for complex syntheses generated from queries.

### 2.2 YAML Frontmatter
Every wiki file (except `index.md` and `log.md`) must begin with a YAML frontmatter block:

```yaml
---
type: entity | concept | summary | synthesis
created: YYYY-MM-DD
updated: YYYY-MM-DD
sources:
  - "Source File or Article Title 1"
  - "Source File or Article Title 2"
tags:
  - domain/topic
  - type/subtype
---
```

### 2.3 Markdown and Links
- **Wikilinks**: Use double-bracket links `[[Wiki Page Name]]` for all cross-references. This is native to Obsidian and keeps formatting clean.
- **Link Text**: Use custom labels when needed: `[[Wiki Page Name|display text]]`.
- **Images**: Refer to local assets using `![[raw/assets/image-name.png]]` or standard markdown `![image](file:///absolute/path/to/raw/assets/image-name.png)`.
- **Headers**: Start each file with a single `# Title` matching the filename. Follow with a standard hierarchy (`##`, `###`).

---

## 3. Core Operations

### 3.1 Ingest (Adding a new source)
When the user places a new source in `raw/` and asks to ingest it:

1. **Analyze**: Read the source document. Understand its core thesis, arguments, entities, and concepts.
2. **Draft Summary**: Create a new page named `wiki/Summary - [Source Name].md`. Document the key takeaways, metadata, and structural summary.
3. **Map & Integrate**:
   - Check `wiki/index.md` to find existing pages related to this source.
   - Update existing pages: Incorporate new findings, call out alignment or contradictions with existing info, and add `[[Summary - Source Name]]` references.
   - Create new concept/entity pages if the source introduces important new topics. Ensure they have frontmatter, descriptions, and backlinks.
4. **Update Index**: Add the new summary page and any new entities/concepts to `wiki/index.md`.
5. **Log the Action**: Append an entry to `wiki/log.md` (see log format below).

### 3.2 Query (Answering questions)
When the user asks a question about the knowledge base:

1. **Locate**: Scan `wiki/index.md` or search files to identify relevant pages.
2. **Synthesize**: Read the identified wiki pages to compile an answer.
3. **Cite**: Cite specific wiki pages using `[[Wiki Page Name]]`.
4. **Persist (Optional)**: If the query requires synthesizing multiple documents, creating a comparison table, or drawing non-trivial connections, ask the user if you should save the output as a `wiki/Synthesis - [Topic].md` page. If so, create the page and update the index and log.

### 3.3 Lint (Health Check)
Periodically or when requested, perform a health check on the wiki:

1. **Identify Orphans**: Find pages with no inbound links (excluding index, log, and schema).
2. **Find Dead Links**: Identify `[[links]]` pointing to pages that do not exist.
3. **Detect Contradictions**: Flag points where different summaries or concept pages assert conflicting information.
4. **Verify Frontmatter**: Check that all wiki pages have valid YAML frontmatter matching Section 2.2.
5. **Report & Fix**: List findings for the user, and offer to automatically fix formatting/broken links.

---

## 4. Special Log Format

The file `wiki/log.md` is an append-only timeline of wiki modifications. Each entry must start with a level-2 header and a specific action tag prefix so it can be parsed easily with command-line tools:

```markdown
## [YYYY-MM-DD] ingest | Summary - Source Title
- Processed raw file: `raw/source-file-name.pdf`
- Created summary page: [[Summary - Source Title]]
- Created/Updated entity/concept pages:
  - [[Entity Name]]
  - [[Concept Name]]

## [YYYY-MM-DD] query | Question about specific topic
- Summarized synthesis for user.
- Saved permanent page: [[Synthesis - Topic Name]]

## [YYYY-MM-DD] lint | Weekly health check
- Audited 42 pages.
- Fixed 2 broken links and corrected frontmatter tags.
```
