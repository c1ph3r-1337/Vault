# Months Calendar Database

<center><div style="margin-bottom: 18px; font-weight: 700; letter-spacing: 0.03em;">Don’t break the chain!</div></center>

> [!Tip]
> Month folders are rendered as a wall-calendar database with note links on each day.

<style>
.months-anchor {
  display: none;
}

.markdown-reading-view:has(.months-anchor) {
  background:
    radial-gradient(760px 420px at 10% 10%, color-mix(in srgb, var(--interactive-accent) 8%, #ffffff), transparent 72%),
    radial-gradient(640px 360px at 90% 88%, color-mix(in srgb, var(--interactive-accent) 6%, #ffffff), transparent 74%),
    linear-gradient(160deg, #e8eaee, #dfe3e8) !important;
}

.wallcal-board {
  width: min(980px, 96%);
  margin: 10px auto;
  padding: 14px 14px 10px;
  border-radius: 16px;
  border: 1px solid #c8ced7;
  background: linear-gradient(180deg, #f4f5f7 0%, #eceff3 100%);
  box-shadow: 0 10px 20px rgba(24, 32, 44, 0.14);
}

.wallcal-stats {
  display: flex;
  flex-wrap: wrap;
  gap: 8px;
  margin: 0 0 12px;
}

.wallcal-chip {
  border-radius: 999px;
  border: 1px solid #cdd3dc;
  background: #f7f8fa;
  color: #2f3540;
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
  border: 1px solid #cfd4dc;
  border-radius: 12px;
  padding: 10px;
  background: #e9ecf1;
  box-shadow: 0 6px 14px rgba(16, 25, 36, 0.1);
}

.wallcal-title {
  text-align: center;
  font-weight: 900;
  letter-spacing: 0.05em;
  font-size: 1.45rem;
  color: color-mix(in srgb, var(--interactive-accent) 86%, #1d8f54);
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
  background: #f0f2f5;
  border: 1px solid #d8dde5;
  border-radius: 4px;
  padding: 4px;
  vertical-align: top;
}

.wallcal-week-head {
  text-align: center;
  font-size: 0.82rem;
  font-weight: 700;
  color: #3d4350;
  background: #dfe3e8 !important;
}

.wallcal-week-label {
  width: 72px;
  text-align: center;
  font-weight: 800;
  letter-spacing: 0.02em;
  color: #2a2f38;
  background: #e4e8ed !important;
}

.wallcal-day-cell {
  min-height: 76px;
  background: #f7f8fa !important;
}

.wallcal-day-number {
  display: block;
  font-size: 1.85rem;
  font-weight: 900;
  line-height: 1;
  margin-bottom: 4px;
  color: #222733;
}

.wallcal-day-number.is-accent {
  color: color-mix(in srgb, var(--interactive-accent) 88%, #179f58);
}

.wallcal-day-notes {
  font-size: 0.74rem;
  line-height: 1.24;
  display: grid;
  gap: 2px;
}

.wallcal-day-link {
  color: #2e3541;
  text-decoration: none;
  border-radius: 5px;
  padding: 1px 3px;
}

.wallcal-day-link:hover,
.wallcal-day-link:focus-visible {
  text-decoration: none;
  background: #e3e8ef;
}

.wallcal-empty {
  opacity: 1;
  background: #e3e7ed !important;
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
const styleId = "months-wallcal-runtime-style-v1";
if (!document.getElementById(styleId)) {
  const style = document.createElement("style");
  style.id = styleId;
  style.textContent = `
    .markdown-reading-view:has(.months-anchor) {
      background:
        radial-gradient(760px 420px at 10% 10%, color-mix(in srgb, var(--interactive-accent) 16%, transparent), transparent 72%),
        radial-gradient(640px 360px at 90% 88%, color-mix(in srgb, var(--interactive-accent) 10%, transparent), transparent 74%),
        linear-gradient(160deg, #0f1318, #151b22) !important;
    }
    .wallcal-board {
      width: min(980px, 96%);
      margin: 10px auto;
      padding: 14px 14px 10px;
      border-radius: 16px;
      border: 1px solid color-mix(in srgb, var(--interactive-accent) 26%, #2a3340);
      background:
        radial-gradient(82% 62% at 8% 8%, color-mix(in srgb, var(--interactive-accent) 16%, transparent) 0%, transparent 74%),
        radial-gradient(72% 58% at 94% 94%, color-mix(in srgb, var(--interactive-accent) 10%, transparent) 0%, transparent 76%),
        linear-gradient(150deg, #151b22, #1b232c);
      box-shadow: 0 12px 26px rgba(0, 0, 0, 0.34);
    }
    .wallcal-stats {
      display: flex;
      flex-wrap: wrap;
      gap: 8px;
      margin: 0 0 12px;
    }
    .wallcal-chip {
      border-radius: 999px;
      border: 1px solid #334050;
      background: #1c2530;
      color: #d9e1ea;
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
      border: 1px solid #364455;
      border-radius: 12px;
      padding: 10px;
      background: #1b232d;
      box-shadow: 0 8px 18px rgba(0, 0, 0, 0.28);
    }
    .wallcal-title {
      text-align: center;
      font-weight: 900;
      letter-spacing: 0.05em;
      font-size: 1.45rem;
      color: color-mix(in srgb, var(--interactive-accent) 88%, #2bd06c);
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
      background: #222b35;
      border: 1px solid #354252;
      border-radius: 4px;
      padding: 4px;
      vertical-align: top;
    }
    .wallcal-week-head {
      text-align: center;
      font-size: 0.82rem;
      font-weight: 700;
      color: #c8d3df;
      background: #27313d !important;
    }
    .wallcal-week-label {
      width: 72px;
      text-align: center;
      font-weight: 800;
      letter-spacing: 0.02em;
      color: #d5dde8;
      background: #293340 !important;
    }
    .wallcal-day-cell {
      min-height: 76px;
      background: #1f2832 !important;
    }
    .wallcal-day-number {
      display: block;
      font-size: 1.85rem;
      font-weight: 900;
      line-height: 1;
      margin-bottom: 4px;
      color: #d9e1ea;
    }
    .wallcal-day-number.is-accent {
      color: color-mix(in srgb, var(--interactive-accent) 88%, #179f58);
    }
    .wallcal-day-notes {
      font-size: 0.74rem;
      line-height: 1.24;
      display: grid;
      gap: 2px;
    }
    .wallcal-day-link {
      color: #d8e0ea;
      text-decoration: none;
      border-radius: 5px;
      padding: 1px 3px;
    }
    .wallcal-day-link:hover,
    .wallcal-day-link:focus-visible {
      text-decoration: none;
      background: #2d3947;
    }
    .wallcal-empty {
      opacity: 1;
      background: #2a3440 !important;
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
  `;
  document.head.appendChild(style);
}

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
      const accentClass = (r === 0 || links.length > 0) ? " is-accent" : "";

      html += `<td class="wallcal-day-cell"><span class="wallcal-day-number${accentClass}">${day}</span>${notesHtml}</td>`;
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
