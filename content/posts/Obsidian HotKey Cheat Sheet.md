---
title: Obsidian Hotkey Cheat Sheet
date: 2026-01-29
tags:
  - obsidian
  - productivity
  - css
  - hotkeys
---
So, I created this cheat sheet for myself and pinned it the the right sidebar and messed around with it until the look matched the rest of my [[obsidian theme]]. This is really just how to get the same look, nothing groundbreaking.
![[Pasted image 20260131100629.png]]

![[Pasted image 20260131111104.png]]

## Overview

- Set up hotkeys in settings
- Create a cheat sheet in your vault
- Icon Shortcodes plugin
- CSS snippet to create pill appearance
- CSS snippet for sidebar tab icons
- Note format

## Setup

Create a note called `Hotkey-Cheat-Sheet.md`, drag and pin it to the right sidebar. You can download [mine](https://github.com/ReutFarkash/obsidian_open_vault/blob/main/03/obsidian/Hotkey%20Cheat%20Sheet.md) and use it as a basis. 

### Required Plugins

**Icon Shortcodes** (Community plugin by [aidenlx](https://github.com/aidenlx/obsidian-icon-shortcodes)). It allows you to insert icons as part of the regular writing flow using shortcodes like `:luc_command:`. Without this, the icons in the note will just render as text. Just type `:` and the first letters of the icon name and you will get options in the suggester. 
I suppose you can skip this and just copy the icons from somewhere if you're not into adding too many community plugins, I actually use it quite often. 

## CSS Snippets

Copy the `hotkey-pills.css` and `sidebar-note-icons.css` snippets, found [here](https://github.com/ReutFarkash/obsidian_open_vault/tree/main/.obsidian/snippets) to `.obsidian/snippets/`:

### 1. `hotkey-pills.css`
This creates the pill appearance for hotkey combinations ![[Pasted image 20260131095103.png|50]]

### 2. `sidebar-note-icons.css`

This replaces the sidebar tab icon with a custom "⌘" symbol

Enable both snippets in Settings → Appearance → CSS snippets.

### The Note Format

I use a callout block with tables and `<span class="hotkey-pill">` wrappers around hotkey combinations to style them into rounded pills. ![[Pasted image 20260131095103.png|50]]

```markdown
> [!note] Hotkeys Cheat Sheet
> 
> <span class="hotkey-pill">:luc_arrow_big_up: :luc_command: .</span> **Show hidden files in finder**


>
> | Navigate back | Navigate forward |
> | -------------------- | ------------------ |
> | <span class="hotkey-pill">:luc_command: :luc_option: :luc_arrow_left:</span> | <span class="hotkey-pill">:luc_command: :luc_option: :luc_arrow_right:</span> |
```


**Why `<span>` instead of markdown?** Callouts don't render custom markdown components well, but HTML spans with classes work reliably in both Live Preview and Reading View.
