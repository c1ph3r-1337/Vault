# Months Database

> [!info] Notes Calendar Database
> Files from your month folders shown as a clean database + calendar view.

```dataview
TABLE WITHOUT ID
  file.folder AS "Month Folder",
  length(rows) AS "Files"
FROM ""
WHERE regexmatch("^\\d+\\.\\s", file.folder)
GROUP BY file.folder
SORT file.folder ASC
```

## Notes by Month

```dataview
TABLE WITHOUT ID
  file.link AS "Note",
  dateformat(file.cday, "ccc, dd LLL yyyy") AS "Created",
  dateformat(file.mday, "ccc, dd LLL yyyy") AS "Updated"
FROM ""
WHERE regexmatch("^\\d+\\.\\s", file.folder)
SORT file.folder ASC, file.cday ASC, file.name ASC
```

## Calendar Board

```dataviewjs
const monthFolders = dv.pages('')
  .where(p => /^\d+\.\s/.test(p.file.folder))
  .sort(p => p.file.cday, 'asc');

const groups = {};
for (const p of monthFolders) {
  const key = p.file.folder;
  if (!groups[key]) groups[key] = [];
  groups[key].push(p);
}

const weekdays = ["Mon", "Tue", "Wed", "Thu", "Fri", "Sat", "Sun"];

for (const [folder, pages] of Object.entries(groups)) {
  dv.header(3, folder);

  const byDay = {};
  for (const p of pages) {
    const d = p.file.cday;
    if (!d) continue;
    const day = d.toFormat("dd");
    const w = d.weekday - 1; // Luxon: Mon=1 ... Sun=7
    if (!byDay[day]) byDay[day] = [];
    byDay[day].push({ link: p.file.link, w });
  }

  let md = `| ${weekdays.join(" | ")} |\n| ${weekdays.map(() => "---").join(" | ")} |\n`;

  const allEntries = [];
  for (const [day, arr] of Object.entries(byDay)) {
    for (const item of arr) allEntries.push({ day, ...item });
  }

  allEntries.sort((a, b) => Number(a.day) - Number(b.day));

  const rows = [];
  let row = Array(7).fill("");
  for (const e of allEntries) {
    const label = `**${e.day}**  \n${e.link}`;
    if (row[e.w]) {
      rows.push(row);
      row = Array(7).fill("");
    }
    row[e.w] = label;
  }
  if (row.some(Boolean)) rows.push(row);

  if (!rows.length) {
    dv.paragraph("_No files with valid create dates in this folder._");
    continue;
  }

  for (const r of rows) {
    md += `| ${r.join(" | ")} |\n`;
  }
  dv.paragraph(md);
}
```
