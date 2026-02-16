# Months Calendar Database

<center><div style="margin-bottom: 18px; font-weight: 700; letter-spacing: 0.03em;">Don’t break the chain!</div></center>

> [!tip]
> Month folders are rendered as a wall-calendar database with note links on each day.

<style>
.months-anchor {
  display: none;
}

.markdown-reading-view:has(.months-anchor) {
  background:
    radial-gradient(720px 380px at 7% 8%, color-mix(in srgb, #19a25a 20%, transparent), transparent 70%),
    radial-gradient(640px 340px at 94% 90%, color-mix(in srgb, #8fd6aa 15%, transparent), transparent 72%),
    linear-gradient(160deg, #151b1f, #1b2228) !important;
}

.wallcal-board {
  width: min(980px, 96%);
  margin: 10px auto;
  padding: 14px;
  border-radius: 18px;
  border: 1px solid color-mix(in srgb, #7bc798 28%, var(--background-modifier-border));
  background:
    radial-gradient(82% 62% at 8% 8%, color-mix(in srgb, #1ca85d 16%, transparent) 0%, transparent 74%),
    radial-gradient(72% 58% at 94% 94%, color-mix(in srgb, #9ce0b7 12%, transparent) 0%, transparent 76%),
    linear-gradient(150deg, color-mix(in srgb, #11161a 90%, var(--background-primary) 10%), color-mix(in srgb, #1a2026 90%, var(--background-secondary) 10%));
  box-shadow: 0 14px 28px rgba(0, 0, 0, 0.25), inset 0 1px 0 rgba(255, 255, 255, 0.06);
}

.wallcal-stats {
  display: flex;
  flex-wrap: wrap;
  gap: 8px;
  margin: 0 0 12px;
}

.wallcal-chip {
  border-radius: 999px;
  border: 1px solid var(--background-modifier-border);
  background: color-mix(in srgb, #182128 78%, var(--background-secondary) 22%);
  color: var(--text-normal);
  font-size: 12px;
  line-height: 1;
  padding: 7px 10px;
  font-weight: 700;
}

.wallcal-grid {
  display: grid;
  gap: 14px;
}

.wallcal-card {
  border: 1px solid color-mix(in srgb, #7bc798 22%, var(--background-modifier-border));
  border-radius: 15px;
  padding: 10px;
  background: linear-gradient(180deg, color-mix(in srgb, #1f2a31 92%, var(--background-secondary) 8%) 0%, color-mix(in srgb, #141a1f 92%, var(--background-primary) 8%) 100%);
  box-shadow: 0 8px 20px rgba(0, 0, 0, 0.2);
}

.wallcal-title {
  text-align: center;
  font-weight: 900;
  letter-spacing: 0.05em;
  font-size: 1.45rem;
  color: color-mix(in srgb, #2ad36a 82%, var(--text-normal));
  margin: 4px 0 10px;
}

.wallcal-table {
  width: 100%;
  border-collapse: separate;
  border-spacing: 3px;
  table-layout: fixed;
}

.wallcal-table th,
.wallcal-table td {
  background: color-mix(in srgb, #212a31 86%, var(--background-secondary-alt));
  border: 1px solid color-mix(in srgb, #d2f3de 16%, var(--background-modifier-border));
  border-radius: 7px;
  padding: 4px;
  vertical-align: top;
}

.wallcal-week-head {
  text-align: center;
  font-size: 0.82rem;
  font-weight: 700;
}

.wallcal-week-label {
  width: 72px;
  text-align: center;
  font-weight: 800;
  letter-spacing: 0.02em;
}

.wallcal-day-cell {
  min-height: 74px;
}

.wallcal-day-number {
  display: block;
  font-size: 1.85rem;
  font-weight: 900;
  line-height: 1;
  margin-bottom: 4px;
  color: color-mix(in srgb, #35da75 84%, var(--text-normal));
}

.wallcal-day-notes {
  font-size: 0.74rem;
  line-height: 1.24;
  display: grid;
  gap: 2px;
}

.wallcal-day-link {
  color: var(--text-normal);
  text-decoration: none;
  border-radius: 5px;
  padding: 1px 3px;
}

.wallcal-day-link:hover,
.wallcal-day-link:focus-visible {
  text-decoration: none;
  background: var(--background-modifier-hover);
}

.wallcal-empty {
  opacity: 0.28;
  min-height: 74px;
}

@media (max-width: 880px) {
  .wallcal-week-label {
    width: 56px;
    font-size: 0.83rem;
  }

  .wallcal-day-number {
    font-size: 1.25rem;
  }
}
</style>

<div class="months-anchor"></div>

```dataviewjs
const monthNameMap = {
  JAN: 1,
  JANUARY: 1,
  FEB: 2,
  FEBRUARY: 2,
  MAR: 3,
  MARCH: 3,
  APR: 4,
  APRIL: 4,
  MAY: 5,
  JUN: 6,
  JUNE: 6,
  JUL: 7,
  JULY: 7,
  AUG: 8,
  AUGUST: 8,
  SEP: 9,
  SEPT: 9,
  SEPTEMBER: 9,
  OCT: 10,
  OCTOBER: 10,
  NOV: 11,
  NOVEMBER: 11,
  DEC: 12,
  DECEMBER: 12,
};

const escapeHtml = (s) => String(s)
  .replaceAll("&", "&amp;")
  .replaceAll("<", "&lt;")
  .replaceAll(">", "&gt;")
  .replaceAll('"', "&quot;")
  .replaceAll("'", "&#39;");

function monthFromFolder(folder) {
  const upper = String(folder || "").toUpperCase();
  for (const [name, num] of Object.entries(monthNameMap)) {
    if (upper.includes(name)) return num;
  }
  return null;
}

function folderLeaf(folderPath) {
  const p = String(folderPath || "");
  const parts = p.split("/").filter(Boolean);
  return parts.length ? parts[parts.length - 1] : p;
}

const weekdayRows = ["SUN", "MON", "TUE", "WED", "THU", "FRI", "SAT"];

const monthFiles = app.vault.getMarkdownFiles().filter((f) => {
  const folder = f.parent?.path || "";
  const leaf = folderLeaf(folder);
  return /^\d+\.\s/.test(leaf);
});

const grouped = {};
for (const file of monthFiles) {
  const folder = file.parent?.path || "";
  if (!grouped[folder]) grouped[folder] = [];
  grouped[folder].push(file);
}

const host = dv.el("div", "", { cls: "wallcal-board" });
const stats = host.createEl("div", { cls: "wallcal-stats" });
const folderNames = Object.keys(grouped).sort();
const totalFiles = Object.values(grouped).reduce((sum, arr) => sum + arr.length, 0);
stats.createEl("span", { cls: "wallcal-chip", text: `${folderNames.length} month folders` });
stats.createEl("span", { cls: "wallcal-chip", text: `${totalFiles} files` });
if (!folderNames.length) {
  stats.createEl("span", {
    cls: "wallcal-chip",
    text: "No month folders detected. Expected names like: 3. FEB",
  });
}

const grid = host.createEl("div", { cls: "wallcal-grid" });

for (const folder of folderNames) {
  const notes = grouped[folder]
    .slice()
    .sort((a, b) => (a.stat?.ctime || 0) - (b.stat?.ctime || 0));

  const detectedMonth = monthFromFolder(folderLeaf(folder));
  const firstWithDate = notes.find((n) => n.stat?.ctime);
  const firstDate = firstWithDate
    ? dv.luxon.DateTime.fromMillis(firstWithDate.stat.ctime)
    : dv.date("today");
  const year = firstDate.year;
  const month = detectedMonth || firstDate.month;

  const firstDay = dv.luxon.DateTime.fromObject({ year, month, day: 1 });
  const daysInMonth = firstDay.daysInMonth;
  const startOffset = firstDay.weekday % 7;
  const weekCols = Math.ceil((startOffset + daysInMonth) / 7);

  const notesByDay = {};
  for (const note of notes) {
    const d = note.stat?.ctime
      ? dv.luxon.DateTime.fromMillis(note.stat.ctime)
      : null;
    if (!d || d.year !== year || d.month !== month) continue;
    if (!notesByDay[d.day]) notesByDay[d.day] = [];
    notesByDay[d.day].push({
      name: note.basename,
      path: note.path,
    });
  }

  const matrix = Array.from({ length: 7 }, () => Array(weekCols).fill(null));
  for (let day = 1; day <= daysInMonth; day++) {
    const absolute = startOffset + (day - 1);
    const col = Math.floor(absolute / 7);
    const row = absolute % 7;
    matrix[row][col] = day;
  }

  let html = "";
  html += `<div class="wallcal-card">`;
  html += `<div class="wallcal-title">${firstDay.toFormat("LLLL yyyy").toUpperCase()}</div>`;
  html += `<table class="wallcal-table">`;
  html += `<thead><tr><th class="wallcal-week-head">WEEK</th>`;
  for (let i = 1; i <= weekCols; i++) html += `<th class="wallcal-week-head">${i}</th>`;
  html += `</tr></thead><tbody>`;

  for (let r = 0; r < 7; r++) {
    html += `<tr>`;
    html += `<th class="wallcal-week-label">${weekdayRows[r]}</th>`;

    for (let c = 0; c < weekCols; c++) {
      const day = matrix[r][c];
      if (!day) {
        html += `<td class="wallcal-empty"></td>`;
        continue;
      }

      const links = notesByDay[day] || [];
      const notesHtml = links.length
        ? `<div class="wallcal-day-notes">${links
            .map((l) => `<a class="wallcal-day-link" href="#" data-path="${escapeHtml(l.path)}">${escapeHtml(l.name)}</a>`)
            .join("")}</div>`
        : "";

      html += `<td class="wallcal-day-cell"><span class="wallcal-day-number">${day}</span>${notesHtml}</td>`;
    }

    html += `</tr>`;
  }

  html += `</tbody></table></div>`;

  const card = grid.createEl("div");
  card.innerHTML = html;
}

host.addEventListener("click", (event) => {
  const a = event.target.closest(".wallcal-day-link");
  if (!a) return;
  event.preventDefault();
  const path = a.getAttribute("data-path");
  if (path) app.workspace.openLinkText(path, "", false);
});
```
