# Quartz Maintenance & Development Notes

This file contains personal technical notes, "cheat sheets" for the Gemini CLI, and future architectural ideas for the Quartz/Obsidian vault.

## 🛠 Current Configuration: Ignoring Drafts

We use a two-layered approach to ensure drafts stay private and don't leak into the public build.

### 1. Quartz Build Ignore (`quartz.config.ts`)
The `ignorePatterns` array in the `configuration` object tells Quartz not to parse or build anything in the `posts/drafts/` directory into your static site.

```typescript
// quartz.config.ts
ignorePatterns: ["posts/drafts", "node_modules", ".obsidian"]
```

### 2. Git/Sync Ignore (`.gitignore`)
To prevent `npx quartz sync` from automatically committing and pushing your drafts to GitHub, we've added the directory to the project's root `.gitignore`.

```text
# .gitignore
content/posts/drafts/
```

---

## 🚀 Gemini Blogging Runbook (User Guide)

Follow these steps to launch Gemini and generate a new technical post.

### 1. Launching the Agent
Always launch Gemini from the `coffeeproject` root with the shared directory included:
```bash
cd /Users/reut/Code/coffeeproject
gemini --include-directories /Users/reut/Code/_shared-gemini
```

### 2. Triggering a Draft
Once inside the Gemini CLI, use a specific mode prompt:
- **Default:** `Draft a technical breakdown on [Subject].`
- **Holistic:** `Write a holistic/narrative post on [Subject] based on [Logs/Link].`
- **Intro Only:** `Create a technical preamble for the [Subject] post.`

### 3. Reviewing the Manifest
The agent creates a folder at `content/posts/drafts/{{title}}/`. Open the `DEVELOPER_MANIFEST.md` inside to check:
- [ ] **Assertions:** Did the AI hallucinate? Verify the "Basis" for its claims.
- [ ] **TODOs:** See the list of required screenshots or GitHub links.

### 4. Adding Assets (Manual Steps)
- **Screenshots:** Take your capture and move it to `content/posts/attachments/`.
- **References:** Update the markdown file using `![[ImageName.png]]`.
- **GitHub:** Upload your code and paste the link into the `[GITHUB]` placeholder in the draft.

### 5. Final Review & Sync
1.  In Gemini, run: `Review the final blog post for [Title].`
2.  Once approved, run: `Finalize the post and move it to the posts directory.`
3.  Deploy to the web from your terminal:
    ```bash
    cd /Users/reut/Code/quartz
    npx quartz sync
    ```
4.  Check the live site to ensure formatting and images render correctly.

---

## 🎯 Future Ideas (To-Do)

- [ ] **Track Drafts but Keep Private:** Explore using a Git Submodule or a separate private repository for the `content/posts/drafts/` folder. This would allow for Git history on drafts without making them public.
- [ ] **Automated GitHub Uploads:** Enhance the blog post skill to automatically propose a `gh gist` or repository upload for code snippets marked with the `[GITHUB]` placeholder.
- [ ] **Obsidian Callout Styling:** Look into custom CSS for the `> [!TODO]` and `> [!INFO]` callouts in the Quartz frontend to make them stand out (or hide them from the public build).
- [ ] **Architecture Evolution:** Move all custom skills to their own dedicated repository and include them in the `coffeeproject` as submodules.
- [ ] **Image Styling:** Tweak CSS for images and screenshots (e.g., add a border or subtle shadow) so they don't meld into the background.
- [ ] **Sidebar Link Styling:** Rethink CSS for the sidebars to handle long links better. Long link wrapping is currently confusing; consider smaller font sizes or adding bullet dots to clearly separate items.
