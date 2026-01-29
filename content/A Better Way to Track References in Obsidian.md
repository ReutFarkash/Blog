---
title: A Better Way to Track References in Obsidian
date: 2026-01-29
tags:
  - obsidian
  - dataview
  - guide
  - productivity
---

I consume a lot of media throughout the day. Whether it's a YouTube video, a Twitter thread, or an article, I often find myself half-remembering something relevant later but struggling to locate the specific source.

My references were scattered everywhere: Notion lists, Obsidian notes, a WhatsApp chat with myself, and a maxed-out "Watch Later" playlist on YouTube. Whenever I wanted to remember or recommend a resource to someone, I’d waste time scrolling through endless bookmarks or just give up entirely.

I tried to solve this using Obsidian's **Tasks** plugin, even attempting to abuse it to log non-task items. But those systems always spiraled out of control or required too much maintenance, so I’d abandon them. I ended up back at square one: either not documenting things, or writing them randomly in a Daily Note where they were hard to find later.

The solution came from an idea I had while organizing book highlights. I realized I could use **Dataview** to query specific lines of text, not just whole files.

This project is the result: a custom script that scans my entire vault for dispersed pages and line items, adjusts for their metadata, and presents the relevant ones in a single, clean table.

## How to Set It Up (The Easy Way)

You don't need to be a coder to use this. You just need to install one plugin and copy a file.

### 1. Requirements
Obsidian, obviously. 

You need the **Dataview** plugin installed.
1.  Go to **Settings > Community Plugins > Browse**.
2.  Search for `Dataview` and install/enable it.

### 2. The Critical Setting
Because this uses advanced logic, you need to enable JavaScript.
1.  Go to **Settings > Dataview**.
2.  Toggle **Enable JavaScript Queries** to ON. 

![[Pasted image 20260129165643.png]]

### 3. Install the Script
1.  Create a folder in your vault called `_utils` (Note that anything here will be disregarded by the script by default).
2.  Inside that, create a folder called `_dataview_scripts`.
3.  Create a new file named `content-metadata-view.js`.
4.  Copy and paste the code from the [content-metadata-view.js](https://github.com/ReutFarkash/_dataview_scripts/blob/main/content-metadata-view.js) into that file.

## How to Use It

The script is designed to "just work." You place a small code block in any note, and it searches for items related to that note or a specific subject you choose.

### Example 1: The "Daily Log" Dashboard
Let's say you are learning React. Throughout the day, you might paste links or thoughts into your Daily Note like this:

```markdown
- Found a great guide on hooks url:: https://react.dev #dev/react
- [ ] Watch the tutorial on context API #dev/react
```

To see all these scattered items in one place, create a new note called "React Dashboard" and paste this code block:

```javascript
```dataviewjs
await dv.view("_utils/_dataview_scripts/content-metadata-view", {
    subject: "#dev/react",
    auto_columns: true
});
```

**The Result:**
The script finds every bullet point tagged `#dev/react` (or nested tags like `#dev/react/hooks`), parses the `url::` into a clickable link, and displays them in a neat table.

![[Pasted image 20260129172707.png]]

### Example 2: Project Management (Mixed View)
This is where the script shines. It handles "Mixed Entities"—showing you both **Files** and **List Items** together.

If you have a project file:
```markdown
---
tags: [project/redesign]
priority: high
---
# Website Redesign
```

And a random thought in a journal:

```markdown
- We should change the font color #project/redesign
```

The script will combine them. The file "Website Redesign" appears because of the frontmatter tag, and the journal note appears because of the inline tag.

```javascript
```dataviewjs
await dv.view("_utils/_dataview_scripts/content-metadata-view", {
    subject: "#project/redesign",
    columns: ["priority", "due_date"]
});
```

![[Pasted image 20260129173343.png]]
![[Pasted image 20260129173428.png]]

### Example 3: collect all items relevant to current page
This is really the default use I created the script for, to collect any random note I left myself regarding the subject anywhere in my vault

![[Pasted image 20260129173734.png]]

## Workflow: Automating the Input with Web Clipper

While the dashboard makes *finding* things easy, *saving* them needs to be frictionless too. I use the official **Obsidian Web Clipper** browser extension to eliminate the boilerplate.

I created a few simple templates that automatically format the link, title, and metadata into a single bullet point. This lets me clip items directly into my **Daily Note**. Once they are there, I can either leave them (and let the script find them later) or move them to a relevant project note.

Here are the templates I use:

### The "Tweet Line" Template
Captures the tweet author and link, adding a `#tweet` tag automatically.
![[Pasted image 20260129174017.png]]
```json
-  {{meta:property:og:title|split:":"|slice:1:|join|markdown}} [source:: [{{title}}]({{url}})] [source::{{author}}] [subject:: {{subject}}]  #tweet [type:: tweet]  [date_added:: [[{{date|date:"YYYY-MM-DD"}}]]] [date_posted:: [[{{selector:article[tabindex="-1"][data-testid="tweet"] a[role="link"] > time?datetime|date:"YYYY-MM-DD"}}]]]
```


### The "Youtube Line" Template

Captures the video title and URL, publication date etc as well as #youtube
![[Pasted image 20260129173853.png]]
```json
-  {{title}} [source:: [{{title}}]({{url}})] [source::{{author}}] [subject:: {{subject}}]  #youtube [type:: youtube]  [date_added:: [[{{date|date:"YYYY-MM-DD"}}]]] [date_posted:: [[{{published|date:"YYYY-MM-DD"}}]]]
```

With these templates, saving a resource takes two clicks. I don't have to worry about "where does this go?"—I just clip it to today's note, add my thoughts, and trust my dashboard to surface it when I need it.

## Under the Hood: How it Works

For the technically inclined, here is how the script handles the logic.

### 1. Unified Aggregation

The script takes a **Subject** (link, tag, or text) and aggregates content based on three rules:

* **Recursive Tags:** Searching `#work` finds `#work/project-a`.
* **Implicit Inheritance:** If a file matches the subject via Frontmatter, all list items inside it are included automatically.
* **Auto-Columns:** Inline fields are parsed into dynamic columns.


### 2. Solving Logic Leaks

A major challenge was Dataview's default tagging behavior. By default, `file.tags` includes tags found *anywhere* in the file. This meant if I tagged a single bullet point `#work` in my Daily Note, the script assumed the *whole file* was about work.

To fix this, I implemented strict checking against `file.frontmatter.tags`. This ensures that file-level inheritance only happens when explicitly defined in the YAML frontmatter.

### 3. Reliability Testing

Since I rely on this for my daily workflow, I needed it to be robust. I set up a two-part testing strategy:

1. **In-Vault Tests:** A folder of static markdown files and a dashboard note to visually verify the table rendering.
2. **External Tests:** A Jest suite running in Node.js to verify the filtering logic (recursion, exclusions, and inheritance) without opening Obsidian.

## Source Code

You can find the script, installation instructions, and the test suite in my repository here:
**[Link to your GitHub Repository](https://github.com/ReutFarkash/_dataview_scripts/tree/main)**
