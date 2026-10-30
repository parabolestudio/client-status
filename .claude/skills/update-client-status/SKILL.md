---
name: update-client-status
description: Create or update Parabole Studio client tracker pages (status.parabole.studio) from the Notion Projects and Tasks databases. Use when asked to update, create or sync a client tracker or status page for a project (e.g. "update Blindspot report", "make a page for Public pages refresh", "update all trackers"), or for the weekly tracker sync.
---

# Parabole client tracker pages

Each tracker is a static, branded HTML page showing one client project's status, timeline, progress, tasks and key documents. Clients get the standalone URL `status.parabole.studio/[slug].html`. The pages live in the GitHub repo `parabolestudio/client-status` (GitHub Pages, deployed from `main`). `index.html` is an internal, noindex list of live trackers, used for navigation only. Never send it to clients.

Work without asking questions. Everything comes from Notion. Only ask which project if the request doesn't say and it isn't a full sync.

## Ground rules

- **Notion is read-only.** Never create, edit or comment on anything in Notion.
- **A project gets a tracker only if** Tracker = `Yes` AND Status is `Planning`, `In Progress` or `Wrapping up`. If a single project is requested and doesn't qualify, say so instead of building it.
- **Never delete page files.** Clients may still have the links. Projects that stop qualifying just come off the index.
- **Keep the design untouched.** Only change the dynamic content: title, client, status, dates, progress, tasks, documents and "Updated" dates. Never rewrite the CSS or structure.
- Show only client-facing material. Leave out internal briefs, internal notes and assignee names.

## Notion sources

- Projects data source: `collection://34b4f30b-d27c-4eda-afc5-a5f86f98b6fd`. Properties: `Project name`, `Client`, `Status`, `Tracker` (select Yes/No), `Dates` (start/end), `Tasks` (relation). The source of truth is the page under **Coordination > Projects**. Ignore same-named pages elsewhere.
- Tasks data source: `collection://a00c90af-3d2f-4671-b6c8-ff52231ee869`. Properties: `Task name`, `Status` (To do / In Progress / Done / Paused / Archived), `Due` (start/end), `Parent-task`, `Sub-tasks`, `Project`.

How to pull:
1. Find the project with rows mode on the Projects source, filtering by `Project name` or by `Tracker` = Yes. `notion-search` also works. Fetch the project page for its properties and body.
2. Get all its tasks in a single call: rows mode on the Tasks source, filter `Project` `relation_contains` the project page URL. Tasks with a `Parent-task` are subtasks; nest them under their parent. Skip Archived tasks. Also skip subtasks whose parent isn't in the project.
3. Key documents: links under the **Key documents** heading of the project page body. Entries like "Drive (coming soon)" or no heading at all mean no documents yet. Use `doc-empty` "Coming soon." in that case. Don't pull internal links from "About this project" unless asked.

## Building a page

Start from the project's existing `[slug].html` if there is one. Otherwise copy `assets/template.html` from this skill. When working in the repo you can instead copy any current tracker, e.g. `blindspot-report.html`. Exact markup for every element is in `references/html-patterns.md`.

- **Slug**: the project name in lowercase kebab-case (`Public pages refresh` → `public-pages-refresh.html`). The `<title>` is `Project Name — Parabole`. Project names are in Title Case.
- **Client line**: `Project with CLIENT`.
- **Status pill**: style depends on status (see patterns). A Completed project gets the dark "Done" pill and 100%.
- **Timeline**: `October 19 — December 4, 2026`, from the project's `Dates`.
- **Progress**: your own estimate from task completion and dates, a round number. Done = 100%. Use an explicit value instead if Evelina gives one.
- **Tasks order**: to do first, then in progress, then done. Within each group, the latest end date comes first. Subtasks within a parent run earliest first.
- **Task states**: use the Notion status, but use judgement on contradictions:
  - A parent marked Done with a subtask still in progress shows as in progress.
  - A parent marked To do with a subtask done or in progress shows as in progress.
  - Flag both in your summary.
- **Done parents are collapsed**: drop their subtasks.
- **"Updated" dates**: set today's date in both places. The status strip uses `Updated Oct 22, 2026`, the footer uses `Updated October 22, 2026`.

## Index

`index.html` lists exactly the qualifying projects. Group them as **Active** (Planning + In Progress) and **Wrapping up**, with counts. Each card shows the date that page was last updated. The footer shows today's date. When a project goes Completed, flip its page to Done/100% once, then remove it from the index.

## Full sync ("update all trackers" / weekly run)

1. Query Projects with Tracker = Yes, and keep only the three active statuses.
2. Update or create every matching page as described above.
3. Flip any page whose project just became Completed to Done.
4. Rewrite the index.
5. Deploy.
6. Summarise for Evelina:
   - pages updated and pages created,
   - anything odd in Notion: tasks past their end date still To do, status contradictions, missing dates.

If the Notion tools aren't available, change nothing and say so.

## Deploy

**With git access to `parabolestudio/client-status`** (a Claude Code session on the repo):
1. Work on a branch, e.g. `git fetch origin main && git checkout -B claude/client-status-skill-file-hjgj3i origin/main`.
2. Commit with a short message such as `Update Blindspot report tracker (Oct 22)`.
3. `git push -u origin <branch>`.
4. `git checkout main && git pull origin main && git merge --no-ff <branch> && git push origin main`.
5. Give Evelina the live URL(s).

**Without git access** (e.g. Claude.ai or Cowork):
1. Read the current page from the repo or from `https://status.parabole.studio/[slug].html` if you can.
2. Produce the finished HTML file(s).
3. Tell Evelina to upload them at github.com/parabolestudio/client-status: **Add file → Upload files**, then **Commit changes**. Same-name files overwrite the old version.
4. Pages go live within a minute. The `fonts/` folder is already in the repo.

## Reply

Keep it short and outcome first:
- the live link(s),
- what changed (status, dates, which tasks moved),
- any Notion oddities,
- any question, such as whether to add a document you weren't sure was client-facing.
