# UX Atelier

A field course in web UX for data storytellers — built for the Parabole team.

Short, playable lessons in the spirit of Brilliant and Duolingo: visual A/B comparisons, tap-the-flaw mockups, scenario questions and quick-fire true/false. Every answer ends with a short *why* and a source, so design feedback can point at a rule instead of a taste.

## Play it

The whole course is a single file with no dependencies.

- **Quickest:** download `index.html` and open it in any browser (works on phones).
- **Or clone:**

  ```sh
  git clone https://github.com/parabolestudio/ux-atelier.git
  open ux-atelier/index.html
  ```

- **Optional hosting:** enable GitHub Pages on this repo (Settings → Pages → deploy from `main`) and the course gets a URL the whole team can bookmark. Note that Pages on a *private* repo requires a paid GitHub plan; on a free plan the repo would need to be public first.

Progress and XP live in memory only — closing the tab resets them. A hosted v2 would persist progress per teammate.

## Curriculum

| # | Module | Status |
|---|--------|--------|
| 1 | **Space & Structure** — spacing scales, alignment, proximity, grouping | 2 of 3 lessons playable |
| 2 | **Type & Hierarchy** — type scales, readable measure, contrast | v2 |
| 3 | **Buttons & Actions** — hierarchy, labels, states, touch targets | 1 of 2 lessons playable |
| 4 | **Forms & Inputs** — labels, validation, helpful errors | v2 |
| 5 | **Filters & Exploration** — batch vs live filtering, chips, empty states, URL state | 1 of 2 lessons playable |
| 6 | **Motion, Feedback & Web Reality** — system status, loading, scrolljacking, performance | v2 |

The **Field guide** tab inside the app collects every rule (including the v2 modules) as a reference — useful during design reviews.

## Sources

Content is grounded in published best practices rather than house opinion:

- [Nielsen Norman Group](https://www.nngroup.com/) — filtering (batch vs. interactive), faceted search, dialogs, empty states, F-pattern scanning, usability heuristics
- [Laws of UX](https://lawsofux.com/) (Jon Yablonski) — Proximity, Common Region, Miller, Hick, Fitts, Jakob, Doherty
- [Interaction Design Foundation](https://www.interaction-design.org/) — Gestalt principles, visual hierarchy
- Apple Human Interface Guidelines & Material Design — touch targets, spacing systems
- [WCAG 2.2](https://www.w3.org/WAI/WCAG22/quickref/) — contrast, target size

## Extending the course

All content lives in plain data arrays near the top of the `<script>` block in `index.html` (`EX_M1L1`, `EX_M1L2`, `EX_M3L1`, `EX_M5L1`, plus `COURSE` and `GUIDE`). An exercise looks like:

```js
{
  type: 'mcq',            // 'mcq' | 'tf' | 'ab' | 'spot'
  prompt: 'The question…',
  options: ['A', 'B'],    // mcq only; ab uses mockA/mockB HTML; spot uses mock HTML with .hot zones
  correct: 1,             // index of the right answer
  rule: 'Short takeaway shown in bold',
  why: 'The explanation shown after answering.',
  src: 'NN/g · article name'
}
```

Add an exercise object to a lesson's array and it appears in the flow, the scoring and the recap automatically. New lessons: add `{id, title, meta, ex}` to a module in `COURSE`.

---

*v1 prototype · 4 lessons, 32 exercises · built August 2026*
