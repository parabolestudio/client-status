# HTML patterns

Copy these exactly. Dates use the `Mon D — Mon D` format (em dash with spaces), e.g. `Oct 19 — Oct 30`. A single day is just `Nov 16`. Undated tasks show `To be scheduled`. Escape `&` as `&amp;` in names.

## Tasks

### To do
```html
<div class="task is-upcoming">
  <div class="task-header">
    <div class="task-check upcoming"></div>
    <div class="task-body">
      <div class="task-title">TASK NAME</div>
      <div class="task-meta mono">Nov 2 — Nov 20</div>
    </div>
    <div class="task-tag upcoming">Up next</div>
  </div>
  <!-- optional .subtasks block -->
</div>
```

### In progress
```html
<div class="task">
  <div class="task-header">
    <div class="task-check current"></div>
    <div class="task-body">
      <div class="task-title">TASK NAME</div>
      <div class="task-meta mono">Oct 19 — Oct 30</div>
    </div>
    <div class="task-tag current">In progress</div>
  </div>
  <!-- optional .subtasks block -->
</div>
```

### Done (always collapsed: no subtasks block)
```html
<div class="task">
  <div class="task-header">
    <div class="task-check done">
      <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="3" stroke-linecap="round" stroke-linejoin="round"><polyline points="20 6 9 17 4 12"></polyline></svg>
    </div>
    <div class="task-body">
      <div class="task-title">TASK NAME</div>
      <div class="task-meta mono">Sep 28 — Oct 9</div>
    </div>
    <div class="task-tag done">Done</div>
  </div>
</div>
```

## Subtasks

Wrap in `<div class="subtasks"> ... </div>` inside the parent `.task`, after `.task-header`. List them in date order (earliest first).

```html
<div class="subtask">
  ICON
  SUBTASK NAME
  <span class="subtask-date mono">Oct 19 — Oct 23</span>
</div>
```

ICON is one of:

- To do (plain circle):
  `<svg class="subtask-icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><circle cx="12" cy="12" r="10"></circle></svg>`
- In progress (circle with filled dot):
  `<svg class="subtask-icon current" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><circle cx="12" cy="12" r="10"></circle><circle cx="12" cy="12" r="4" fill="currentColor" stroke="none"></circle></svg>`
- Done (check):
  `<svg class="subtask-icon done" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><polyline points="20 6 9 17 4 12"></polyline></svg>`

## Key documents

```html
<a href="URL" target="_blank" class="doc-item">
  <div class="doc-icon">ICON SVG</div>
  <div class="doc-info">
    <div class="doc-name">DOCUMENT NAME</div>
    <div class="doc-desc">SHORT DESCRIPTION</div>
  </div>
  <div class="doc-arrow">
    <svg width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><line x1="7" y1="17" x2="17" y2="7"></line><polyline points="7 7 17 7 17 17"></polyline></svg>
  </div>
</a>
```

Icons:
- Figma (figma.com):
  `<svg width="16" height="16" viewBox="0 0 24 24" fill="currentColor"><path d="M12 2H8.5A3.5 3.5 0 005 5.5 3.5 3.5 0 008.5 9H12V2z"/><path d="M12 9H8.5A3.5 3.5 0 005 12.5 3.5 3.5 0 008.5 16H12V9z"/><path d="M12 2h3.5A3.5 3.5 0 0119 5.5 3.5 3.5 0 0115.5 9H12V2z"/><path d="M19 12.5A3.5 3.5 0 0015.5 9H12v3.5a3.5 3.5 0 007 0z"/><path d="M8.5 16a3.5 3.5 0 100 7 3.5 3.5 0 000-7z"/></svg>`
- Folder (Drive folders, Docs, anything else):
  `<svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M22 19a2 2 0 01-2 2H4a2 2 0 01-2-2V5a2 2 0 012-2h5l2 3h9a2 2 0 012 2z"></path></svg>`
- Sheet (Google Sheets, spreadsheets):
  `<svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><rect x="3" y="3" width="18" height="18" rx="2" ry="2"></rect><line x1="3" y1="9" x2="21" y2="9"></line><line x1="3" y1="15" x2="21" y2="15"></line><line x1="9" y1="3" x2="9" y2="21"></line></svg>`
- Live page / website:
  `<svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><circle cx="12" cy="12" r="10"></circle><line x1="2" y1="12" x2="22" y2="12"></line><path d="M12 2a15.3 15.3 0 0 1 4 10 15.3 15.3 0 0 1-4 10 15.3 15.3 0 0 1-4-10 15.3 15.3 0 0 1 4-10z"></path></svg>`

No client-facing documents yet: the whole card body is `<div class="doc-empty">Coming soon.</div>`.

## Status pill (status strip)

```html
<div class="status-pill" style="STYLE">
  <span class="dot"></span>
  STATUS TEXT
</div>
```

| Status | STYLE | Text |
|---|---|---|
| Planning | `background: var(--moss)` | Planning |
| In Progress | `background: var(--accent-yellow)` | In Progress |
| Wrapping up | `background: var(--warm-gray)` | Wrapping up |
| Completed | `background: var(--off-black); color: var(--paper-white)` and the dot gets `style="background: var(--paper-white); opacity: 0.6;"` | Done |

## Index card (index.html)

Pill classes: `planning`, `in-progress`, `wrapping`.

```html
<a href="SLUG.html" class="card">
  <div class="card-body">
    <div class="card-title">PROJECT NAME</div>
    <div class="card-client">CLIENT</div>
  </div>
  <div class="card-meta">
    <span class="card-updated mono">Oct 22</span>
    <span class="status-pill in-progress"><span class="dot"></span>In Progress</span>
  </div>
  <div class="card-arrow">
    <svg width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><line x1="7" y1="17" x2="17" y2="7"></line><polyline points="7 7 17 7 17 17"></polyline></svg>
  </div>
</a>
```

Groups: `Active` (Planning + In Progress) and `Wrapping up`, each with a `<span class="group-count mono">N</span>`. Leave out a group with no projects. Footer: `Updated Month D, YYYY`.
