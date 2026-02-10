```dataviewjs
// Title shown above the heatmap(s).
dv.paragraph("**🔗 Writing – Don’t break the chain! 🔗🔗🔗🔗**");

// 1) Define accepted folder paths.
// Your vault may expose folders as "Daily Wins" (common) or "Crypto/Daily Wins".
const CANDIDATE_FOLDERS = new Set(["Daily Wins", "Crypto/Daily Wins"]);

// 2) Scan all pages, then filter by exact folder + writing:true.
// "writing" is treated as boolean-like to handle YAML/parser differences.
const allPages = dv.pages();
const pages = allPages.where((p) => {
  const folderOk = CANDIDATE_FOLDERS.has(p.file.folder);
  const writingVal = p.writing;
  const writingOk = writingVal === true || writingVal === "true" || writingVal === 1;
  return folderOk && writingOk;
});

// DataviewJS runs in an eval context, so avoid top-level "return".
if (pages.length === 0) {
  dv.paragraph('No notes found in "Daily Wins" (or "Crypto/Daily Wins") with writing: true.');
} else {
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
  } else {
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
  }
}



  const rows = dv.pages()
    .where(p => p.file.folder.includes("Daily Wins"))
    .map(p => [p.file.path, p.file.folder, p.writing, typeof p.writing, p.created, p.file.mtime]);

  dv.table(["path", "folder", "writing", "type", "created", "mtime"], rows);
```
