```dataviewjs
// Title shown above the heatmap(s).
dv.paragraph("**🔗 Writing – Don’t break the chain! 🔗🔗🔗🔗**");

// 1) Only scan notes inside this folder.
// If your vault root is already "Crypto", change this to "Daily Wins".
const TARGET_FOLDER = "Crypto/Daily Wins";

// 2) Pull only pages from the target folder and keep only writing notes.
const pages = dv.pages(`"${TARGET_FOLDER}"`).where((p) => p.writing === true);

// Early exit if nothing matches.
if (pages.length === 0) {
  dv.paragraph(`No notes found in "${TARGET_FOLDER}" with writing: true.`);
  return;
}

// 3) Group notes by year/day.
// Date priority: frontmatter "created" first, then file.mtime fallback.
const byYear = new Map(); // year -> Map(dateKey -> {count, links})

for (const page of pages) {
  const rawDate = page.created ?? page.file.mtime;
  const dateObj = dv.date(rawDate);
  if (!dateObj) continue; // skip invalid/missing dates safely

  const dateKey = dateObj.toFormat("yyyy-MM-dd");
  const year = Number(dateObj.toFormat("yyyy"));

  if (!byYear.has(year)) byYear.set(year, new Map());
  const dayMap = byYear.get(year);

  if (!dayMap.has(dateKey)) dayMap.set(dateKey, { count: 0, links: [] });
  const day = dayMap.get(dateKey);

  day.count += 1;
  day.links.push(`[[${page.file.path}|${page.file.name}]]`);
}

if (byYear.size === 0) {
  dv.paragraph("No valid dates found to plot on the heatmap.");
  return;
}

// 4) Build Heatmap Calendar data structure and render.
// We render one heatmap per year so all notes are represented.
const years = [...byYear.keys()].sort((a, b) => b - a);

for (const year of years) {
  const dayMap = byYear.get(year);

  const entries = [...dayMap.entries()]
    .map(([date, value]) => ({
      date, // required by Heatmap Calendar
      intensity: value.count, // higher value = stronger color
      content: value.links.join("<br>"), // tooltip content
    }))
    .sort((a, b) => a.date.localeCompare(b.date));

  const calendarData = {
    year,
    entries,
  };

  dv.header(4, `Writing Activity ${year}`);
  renderHeatmapCalendar(this.container, calendarData);
}
```
