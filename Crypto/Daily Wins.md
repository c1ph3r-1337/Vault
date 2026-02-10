```dataviewjs
// Title shown above the heatmap(s).
dv.paragraph("**🔗 Writing – Don’t break the chain! 🔗🔗🔗🔗**");

// 1) Find the right folder path for this vault.
// Some vaults use "Daily Wins", others "Crypto/Daily Wins".
const CANDIDATE_FOLDERS = ["Daily Wins", "Crypto/Daily Wins"];
let TARGET_FOLDER = null;
let pages = dv.pages('""').where(() => false); // empty by default

for (const folder of CANDIDATE_FOLDERS) {
  const matches = dv.pages(`"${folder}"`).where((p) => p.writing === true);
  if (matches.length > 0) {
    TARGET_FOLDER = folder;
    pages = matches;
    break;
  }
}

// If no notes matched writing:true, use the first existing folder for clear messaging.
if (!TARGET_FOLDER) {
  for (const folder of CANDIDATE_FOLDERS) {
    const anyInFolder = dv.pages(`"${folder}"`);
    if (anyInFolder.length > 0) {
      TARGET_FOLDER = folder;
      break;
    }
  }
}

// DataviewJS runs in an eval context, so avoid top-level "return".
if (pages.length === 0) {
  dv.paragraph(
    `No notes found in "${TARGET_FOLDER ?? CANDIDATE_FOLDERS[0]}" with writing: true.`
  );
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
```
