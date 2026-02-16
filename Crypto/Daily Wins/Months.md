# Months Calendar Database

> [!tip] Calendar View
> Month folders are shown in a wall-calendar style like your reference image.

<style>
.wallcal-grid {
  display: grid;
  gap: 18px;
}

.wallcal-card {
  border: 1px solid var(--background-modifier-border);
  border-radius: 14px;
  padding: 12px;
  background: linear-gradient(180deg, var(--background-secondary) 0%, var(--background-primary) 100%);
  box-shadow: 0 4px 16px rgba(0, 0, 0, 0.08);
}

.wallcal-title {
  text-align: center;
  font-weight: 800;
  letter-spacing: 0.04em;
  font-size: 1.3rem;
  color: var(--text-accent);
  margin: 6px 0 10px;
}

.wallcal-table {
  width: 100%;
  border-collapse: separate;
  border-spacing: 3px;
  table-layout: fixed;
}

.wallcal-table th,
.wallcal-table td {
  background: var(--background-secondary-alt);
  border: 1px solid var(--background-modifier-border);
  border-radius: 8px;
  padding: 4px;
  vertical-align: top;
}

.wallcal-week-label {
  width: 80px;
  text-align: center;
  font-weight: 700;
  letter-spacing: 0.02em;
}

.wallcal-week-head {
  text-align: center;
  font-size: 0.85rem;
  font-weight: 700;
}

.wallcal-day-cell {
  min-height: 70px;
}

.wallcal-day-number {
  display: block;
  font-size: 1.6rem;
  font-weight: 800;
  line-height: 1;
  margin-bottom: 4px;
  color: var(--text-normal);
}

.wallcal-day-notes {
  font-size: 0.75rem;
  line-height: 1.25;
  display: grid;
  gap: 2px;
}

.wallcal-empty {
  opacity: 0.35;
  min-height: 70px;
}

@media (max-width: 900px) {
  .wallcal-day-number {
    font-size: 1.2rem;
  }

  .wallcal-week-label {
    width: 58px;
    font-size: 0.85rem;
  }
}
</style>

```dataview
TABLE WITHOUT ID
  file.folder AS "Month Folder",
  length(rows) AS "Files"
FROM ""
WHERE regexmatch("^\\d+\\.\\s", file.folder)
GROUP BY file.folder
SORT file.folder ASC
```

```dataviewjs
const monthNameMap = {
  JAN: 1,
  FEB: 2,
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
  SEP: 9,
  SEPT: 9,
  SEPTEMBER: 9,
  OCT: 10,
  NOV: 11,
  DEC: 12
};

function monthFromFolder(folder) {
  const upper = folder.toUpperCase();
  for (const [k, v] of Object.entries(monthNameMap)) {
    if (upper.includes(k)) return v;
  }
  return null;
}

const weekdayRows = ["SUN", "MON", "TUE", "WED", "THU", "FRI", "SAT"];
const pages = dv.pages("")
  .where(p => /^\d+\.\s/.test(p.file.folder));

const grouped = {};
for (const p of pages) {
  const key = p.file.folder;
  if (!grouped[key]) grouped[key] = [];
  grouped[key].push(p);
}

const root = dv.el("div", "");
root.className = "wallcal-grid";

for (const folder of Object.keys(grouped).sort()) {
  const notes = grouped[folder].sort((a, b) => a.file.cday.ts - b.file.cday.ts);
  const detectedMonth = monthFromFolder(folder);

  const firstWithDate = notes.find(n => n.file.cday);
  const year = firstWithDate ? firstWithDate.file.cday.year : dv.date("today").year;
  const month = detectedMonth || (firstWithDate ? firstWithDate.file.cday.month : dv.date("today").month);

  const firstDay = dv.luxon.DateTime.fromObject({ year, month, day: 1 });
  const daysInMonth = firstDay.daysInMonth;
  const startOffset = firstDay.weekday % 7; // Sun=0, Mon=1, ... Sat=6
  const weekCols = Math.ceil((startOffset + daysInMonth) / 7);

  const noteByDay = {};
  for (const n of notes) {
    const d = n.file.cday;
    if (!d || d.year !== year || d.month !== month) continue;
    if (!noteByDay[d.day]) noteByDay[d.day] = [];
    noteByDay[d.day].push(n.file.link);
  }

  const matrix = Array.from({ length: 7 }, () => Array(weekCols).fill(null));
  for (let day = 1; day <= daysInMonth; day++) {
    const absolute = startOffset + (day - 1);
    const col = Math.floor(absolute / 7);
    const row = absolute % 7;
    matrix[row][col] = day;
  }

  const monthTitle = firstDay.toFormat("LLLL yyyy").toUpperCase();

  let html = `<div class="wallcal-card">`;
  html += `<div class="wallcal-title">${monthTitle}</div>`;
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

      const links = noteByDay[day] || [];
      const notesHtml = links.length
        ? `<div class="wallcal-day-notes">${links.map(l => `<div>${l}</div>`).join("")}</div>`
        : "";

      html += `<td class="wallcal-day-cell"><span class="wallcal-day-number">${day}</span>${notesHtml}</td>`;
    }

    html += `</tr>`;
  }

  html += `</tbody></table></div>`;

  const card = dv.el("div", "");
  card.innerHTML = html;
  root.appendChild(card);
}
```
