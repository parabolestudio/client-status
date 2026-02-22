---
name: update-client-status
description: "Generate an HTML client status page for Parabole Studio. IMMEDIATELY start working: read the template file, pull all data from Notion, generate HTML, save it. Do NOT use AskUserQuestion — everything is pre-configured. The only acceptable question is which project name if not provided."
---

# Client Status Page Generator — Parabole Studio

You are generating a client-facing HTML status page for Parabole Studio (parabole.studio). The user is Evelina (evelina@parabole.studio), studio founder.

## CRITICAL RULES — read these first

**DO NOT ASK CLARIFYING QUESTIONS.** Do not use AskUserQuestion. Do not ask about format, style, content, or preferences. Everything is already defined below. Just execute the workflow.

1. **The output is ALWAYS an HTML file.** Never ask what format the user wants — it is always HTML. Never edit, create, or modify anything in Notion. Notion is read-only — you pull data from it, nothing more.
2. **Always start by reading the existing template.** Before generating anything, use the Read tool to read `client-status-pages/client-status-page.html` from the user's selected folder. Copy its entire `<style>` block and page structure exactly. Only replace the content sections (header text, status, overview data, tasks, documents). Do NOT rebuild the CSS or HTML structure from scratch.
3. **If you cannot find the template file**, ask Evelina where it is before proceeding. Do not attempt to recreate it from memory.
4. **Do not ask the user for data you already have.** If you successfully pulled project data, tasks, and key documents from Notion, go straight to generating the HTML. Do not ask the user to confirm, re-provide, or replace the data. Just use what Notion gave you.
5. **This is a one-shot workflow.** Read template → fetch ALL data from Notion (project details, tasks, key documents) → generate HTML → save file. Do NOT stop to ask questions between these steps. The ONLY question you may ever ask is which project to update (and only if the user didn't already say).
6. **Skip all clarifying questions.** Do not ask about format (it's HTML), style (it's in the template), content (it comes from Notion), or anything else. Just do the work.

## Objective

Generate or update a branded, single-file HTML status page for a specific client project. The page is meant to be shared with clients via a URL so they can track project progress without needing Notion access.

## Step 1: Read the template

Use the Read tool to read `client-status-pages/client-status-page.html` from the user's selected folder. This is your base. You will keep all of its CSS, `@font-face` declarations, logo SVG, and HTML structure intact. You only replace the dynamic content:
- Project title and client name in the header
- Status pill text and color
- Timeline dates and progress percentage
- The list of tasks in the tasks card
- The list of documents in the documents card
- The "Updated" date in the status strip and footer

## Step 2: Gather project data

Ask Evelina which project to update (or she will tell you). Then pull the data from Notion.

**You MUST use Notion to get project data. NEVER ask Evelina for project details (status, timeline, completion, tasks, documents) — all of this lives in Notion and must be fetched automatically.** The only question you may ask is which project to update, and only if she didn't already say.

### Pull from Notion
Use the Notion MCP tools (notion-search, notion-fetch). **Notion is read-only — never write to it.**

1. **Find the project**: Use `notion-search` to search for the project by name. You will likely get multiple results — **you must pick the right one.** The rules:
   - The correct page is ALWAYS the one that lives inside the **"Projects" database** under the **"Coordination"** page. This is the only source of truth.
   - **IGNORE** any other pages with the same name that live elsewhere (e.g., under client pages, meeting notes, or other sections of the workspace). These are duplicates or references — do not use them.
   - To identify the correct page: check the parent property of each search result. The right one will have a parent that is the "Projects" database or will show "Coordination" in its breadcrumb/path.
   - If you're unsure which is which, fetch each candidate and look at its properties — the correct project page will have properties like "Client", "Status", "Dates", "Completion", "Lead designer", "Priority", and "Scale".

2. **Fetch the project page** using `notion-fetch` with the page ID. Extract:
   - Project name and client name (from page title and "Client" property)
   - Status property (Planning / In Progress / Wrapping up)
   - Timeline dates (from "Dates" property — start and end)
   - Completion percentage (from "Completion" property)

3. **Fetch the project tasks**: The project page contains a "Project tasks" inline database/board. Each task has:
   - Task name (title)
   - Status: "To do", "In Progress", or "Done"
   - Date range
   - Assignee
   - Subtasks: these appear as linked pages or sub-items within the task. Fetch individual task pages if needed to get subtask details.

4. **Extract Key Documents**: The project page body has a section titled **"Key documents"**. Below this heading are linked items — typically Notion page links or bookmarks pointing to external resources (Figma, Google Drive, spreadsheets, etc.). To extract:
   - After fetching the project page content, look for a heading block containing "Key documents"
   - The items below it are child blocks — they may be `link_to_page`, `bookmark`, `bulleted_list_item`, or `paragraph` blocks containing links
   - For `link_to_page` blocks: fetch the linked page to find any URL property or bookmark/link inside it. The page title becomes the document name.
   - For `bookmark` blocks: the URL is directly available
   - For text blocks with links: extract the URL from the rich text
   - For each document, determine an icon type from the URL or name: "figma" (figma.com), "sheet" (sheets/spreadsheets), "folder" (drive.google.com/drive/folders), "pdf" (.pdf)
   - If a linked item is a Notion page with no external URL, use its Notion URL as the link

5. Optionally check **"What we know so far"** or **"About this project"** sections for additional document links.

### Fallback ONLY if Notion tools are completely unavailable
If the Notion MCP connector is not connected at all (no notion-search or notion-fetch tools exist), then — and ONLY then — ask Evelina to provide: project name, client name, status, timeline, completion %, tasks with statuses and dates, and key document links. But this should almost never happen. If the tools exist, use them.

## Step 3: Generate the HTML page

Take the template you read in Step 1 and replace only the dynamic content sections with the data from Step 2. Follow these rules:

### Content mapping
- `<h1 class="project-title">` → project name
- `<p class="project-client">` → "Project with [CLIENT NAME]" (always use "with", not "for")
- Status pill text → the project status
- `.status-updated` span → "Updated [today's date]"
- `.timeline-dates` → formatted date range
- `.progress-fill` width → completion percentage
- `.progress-pct` → completion percentage text
- `.tasks-card` contents → rebuilt from task data (see patterns below)
- `.documents-card` contents → rebuilt from key documents data (see patterns below)
- `.footer-updated` → "Updated [today's date]"

### Status pill color
Change the `.status-pill` inline style based on project status:
- **In Progress**: `background: var(--accent-yellow)` (default in template)
- **Planning**: `background: var(--moss)`
- **Wrapping up**: `background: var(--warm-gray)`
- **Done**: `background: var(--off-black); color: var(--paper-white)`

### Task ordering
Sort tasks **newest first**: upcoming/to-do at the top, in-progress in the middle, completed at the bottom. Within each status group, sort by date (most recent first).

### Task HTML patterns

**To do / Up next:**
```html
<div class="task is-upcoming">
  <div class="task-header">
    <div class="task-check upcoming"></div>
    <div class="task-body">
      <div class="task-title">TASK NAME</div>
      <div class="task-meta mono">DATE RANGE</div>
    </div>
    <div class="task-tag upcoming">Up next</div>
  </div>
</div>
```

**In progress (with optional subtasks):**
```html
<div class="task">
  <div class="task-header">
    <div class="task-check current"></div>
    <div class="task-body">
      <div class="task-title">TASK NAME</div>
      <div class="task-meta mono">DATE RANGE</div>
    </div>
    <div class="task-tag current">In progress</div>
  </div>
  <div class="subtasks">
    <div class="subtask">
      <svg class="subtask-icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><circle cx="12" cy="12" r="10"></circle></svg>
      SUBTASK NAME
    </div>
  </div>
</div>
```

**Done (with optional subtasks):**
```html
<div class="task">
  <div class="task-header">
    <div class="task-check done">
      <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="3" stroke-linecap="round" stroke-linejoin="round"><polyline points="20 6 9 17 4 12"></polyline></svg>
    </div>
    <div class="task-body">
      <div class="task-title">TASK NAME</div>
      <div class="task-meta mono">DATE RANGE</div>
    </div>
    <div class="task-tag done">Done</div>
  </div>
  <div class="subtasks">
    <div class="subtask">
      <svg class="subtask-icon done" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><polyline points="20 6 9 17 4 12"></polyline></svg>
      SUBTASK NAME
    </div>
  </div>
</div>
```

### Document link pattern
```html
<a href="URL" class="doc-item">
  <div class="doc-icon figma"><!-- classes: figma, sheet, folder, pdf -->
    <!-- appropriate SVG icon -->
  </div>
  <div class="doc-info">
    <div class="doc-name">DOCUMENT NAME</div>
    <div class="doc-desc">SHORT DESCRIPTION</div>
  </div>
  <div class="doc-arrow">
    <svg width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><line x1="7" y1="17" x2="17" y2="7"></line><polyline points="7 7 17 7 17 17"></polyline></svg>
  </div>
</a>
```

Icon type classes and their SVGs:
- `figma`: Figma logo SVG
- `sheet`: grid/table SVG
- `folder`: folder SVG
- `pdf`: document SVG

All icon containers use: `background: var(--warm-gray); color: var(--lake-gray)`

## Step 4: Save the output

1. Save as `client-status-pages/[project-slug].html` in the user's selected folder (e.g., `client-status-pages/competitor-intelligence.html`)
2. The `client-status-pages/fonts/` folder should already contain the font files (Safiro and Simplon Mono OTF). If it doesn't exist, warn Evelina.
3. Provide a link to the generated file.

## Step 5: Deploy to GitHub Pages

The status pages are hosted on GitHub Pages at **status.parabole.studio**. After saving the HTML file, it needs to be uploaded to the GitHub repository to go live.

- **Repository**: `parabolestudio/client-status` (https://github.com/parabolestudio/client-status)
- **Live URL pattern**: `status.parabole.studio/[filename].html`

Tell Evelina the file is ready and ask her to upload it:
1. Go to https://github.com/parabolestudio/client-status
2. Click **"Add file" → "Upload files"**
3. Drag the new/updated HTML file from the `client-status-pages` folder
4. Click **"Commit changes"**

The page will be live within a minute at `status.parabole.studio/[project-slug].html`.

If updating an existing page (same filename), the upload will overwrite the old version automatically.
