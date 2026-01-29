---
title: Building a Robust Content Aggregator for Obsidian
date: 2026-01-29
tags:
  - obsidian
  - dataview
  - javascript
  - productivity
  - engineering
---

Obsidian is exceptional at linking ideas, but visualizing the relationships between **pages** (files) and **atomic concepts** (list items) can sometimes be a challenge. Standard Dataview queries (DQL) force you to choose: do you want a table of *Pages* or a list of *Tasks*?

I wanted both. I wanted a "Mixed Entity" view that could aggregate everything related to a specific subject—whether it was a dedicated project file or a stray bullet point in a daily note—into a single, unified dashboard.

Here is how I engineered `content-metadata-view.js`, a robust DataviewJS script, and why I treated it like a software product with a dedicated test suite.

## The Problem: Granularity Mismatch

In my vault, I use tags and links recursively. If I have a tag `#work/project-a`, I often list items under it:

```markdown
# Daily Note
- Had a meeting with [[Alice]] about the roadmap #work/project-a
```

But I also have a dedicated file:

```yaml
***
tags: [work/project-a]
***
# Project A Strategy Document
```

A standard query for `#work` usually misses the list item (because it's looking for pages) or the page (because it's looking for items). Furthermore, metadata handling in Dataview is tricky. If I add inline data like `priority:: high` to a bullet point, visualizing that alongside page-level metadata requires complex flattening.

## The Solution: A Unified Metadata View

I developed a script that standardizes these entities. It scans the vault for a **Subject** (a link, a tag, or a text string) and performs three logical checks:

1. **Implicit Inheritance:** If a *File* matches the subject (via Frontmatter tags), all list items within it are inherently considered relevant.
2. **Recursive Tagging:** A search for `#parent` automatically captures `#parent/child`, respecting the hierarchy of the vault.
3. **Auto-Column Generation:** The script detects inline fields (`key:: value`) on the fly and generates dynamic table columns, normalizing Booleans to checkboxes (✅) and URLs to clickable links.

### The "Leak" in the Logic

During development, we encountered a fascinating edge case regarding inheritance. Initially, the script checked `file.tags` to decide if a file matched the subject. However, Dataview aggregates *all* tags in a file into `file.tags`—including tags found inside list items.

This caused a logic leak: if a single bullet point had `#work`, the script assumed the *entire file* was about `#work`, causing unrelated personal tasks in that Daily Note to appear in the work dashboard.

**The Fix:** We implemented a strict check against `file.frontmatter.tags` (or `file.etags`). Only explicit file-level tags trigger inheritance. This distinction between *Explicit* vs. *Implicit* metadata was crucial for accuracy.

## Engineering Reliability: The Test Suite

Because this script is central to my workflow, I couldn't rely on "eye-balling" it to ensure it worked. I adopted a dual-layer testing strategy often seen in professional software development, adapted for Obsidian.

### 1. In-Vault Visual Verification

I created a dedicated `_tests` folder in my vault containing "Fixed State" markdown files. A dashboard note (`Test Suite Summary`) runs the script against this static data to visually verify that tables render as expected.

### 2. External Unit Testing (Jest)

To verify the logic without the overhead of Obsidian's rendering engine, I extracted the logic and wrapped it in a standard Node.js environment using **Jest**.

I mocked the Dataview API (`dv.pages`, `dv.current`) to simulate vault data structures. This allows me to run:

```bash
npm test
```

And instantly verify recursive logic, exclusion filters, and metadata parsing in milliseconds.

## How to Use It

The script is designed to be "drop-in" ready. You can find the source code on my GitHub (link below).

**Installation:**

1. Save the script to `_utils/_dataview_scripts/content-metadata-view.js`.
2. Insert this block in any note:
```javascript
// Shows everything related to the current file name
await dv.view("_utils/_dataview_scripts/content-metadata-view");
```

**Advanced Configuration:**

```javascript
await dv.view("_utils/_dataview_scripts/content-metadata-view", {
    subject: "#person",       // Override subject
    auto_columns: true,       // dynamic columns
    exclude_current: true     // Hide the active file
});
```


## Credits \& Resources

This project stands on the shoulders of the incredible Obsidian community.

* **[Obsidian](https://obsidian.md/):** For providing the extensible platform.
* **[Dataview](https://github.com/blacksmithgu/obsidian-dataview):** The plugin by Michael Brenan that makes all of this possible. The `dv.view()` function is the engine behind this modular approach.
* **[Quartz](https://quartz.jzhao.xyz/):** For the static site generator hosting this blog, created by Jacky Zhao.

> [!INFO] Source Code
> You can find the script and the full test suite in my repository here: **[Link to your Repo]**

If you are struggling to unify your pages and bullets in Obsidian, give this script a try. It turns your vault from a collection of text files into a relational database, without losing the flexibility of Markdown.

```
<span style="display:none">[^1][^10][^11][^12][^13][^14][^15][^16][^17][^18][^19][^2][^20][^21][^22][^3][^4][^5][^6][^7][^8][^9]</span>

<div align="center">⁂</div>

[^1]: image.jpg
[^2]: image.jpg
[^3]: image.jpg
[^4]: image.jpg
[^5]: image.jpg
[^6]: image.jpg
[^7]: image.jpg
[^8]: image.jpg
[^9]: image.jpg
[^10]: image.jpg
[^11]: image.jpg
[^12]: image.jpg
[^13]: image.jpg
[^14]: image.jpg
[^15]: image.jpg
[^16]: image.jpg
[^17]: image.jpg
[^18]: image.jpg
[^19]: image.jpg
[^20]: image.jpg
[^21]: Dataview-Script-Content-Metadata-View.md
[^22]: content-metadata-view.js```

