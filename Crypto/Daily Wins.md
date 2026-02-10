<center>🔗 Don’t break the chain! 🔗</center>

```dataviewjs
// Show all weekdays on the left and hide the plugin's sparse labels.
const styleId = "writing-heatmap-weekday-sidebar";
if (!document.getElementById(styleId)) {
  const style = document.createElement("style");
  style.id = styleId;
  style.textContent = `
    .writing-heatmap-wrap {
      display: flex;
      align-items: flex-start;
      gap: 10px;
      margin-bottom: 12px;
    }
    .writing-heatmap-weekdays {
      display: flex;
      flex-direction: column;
      gap: 4px;
      min-width: 34px;
      padding-top: 30px;
      font-size: 12px;
      line-height: 12px;
      opacity: 0.85;
      user-select: none;
    }
    .writing-heatmap-weekdays > div {
      height: 12px;
    }
    .writing-heatmap-wrap .heatmap-calendar-weekdays,
    .writing-heatmap-wrap .heatmap-calendar-yaxis,
    .writing-heatmap-wrap [class*="weekday"] {
      display: none !important;
    }
  `;
  document.head.appendChild(style);
}

// 1) Folder matcher:
// supports "Daily Wins", "Crypto/Daily Wins", "Vault/Crypto/Daily Wins", etc.
const isDailyWinsFolder = (folder) =>
  folder === "Daily Wins" || folder.endsWith("/Daily Wins");
const IST_ZONE = "Asia/Kolkata";

// 2) Scan all pages, then filter by folder + writing:true.
// "writing" is treated as boolean-like to handle YAML/parser differences.
const allPages = dv.pages();
const pages = allPages.where((p) => {
  const folderOk = isDailyWinsFolder(p.file.folder);
  const writingVal = p.writing;
  const writingOk = writingVal === true || writingVal === "true" || writingVal === 1;
  return folderOk && writingOk;
});

// DataviewJS runs in an eval context, so avoid top-level "return".
if (pages.length === 0) {
  dv.paragraph('No notes found in a "/Daily Wins" folder with writing: true.');
} else {
  // 3) Group notes by year/day.
  // Date priority: frontmatter "created" first, then file.mtime fallback.
  const byYear = new Map(); // year -> Map(dateKey -> {count, links})

  for (const page of pages) {
    // Prefer frontmatter "created"; otherwise use file modified time.
    // Normalize to IST so day cells follow India time.
    let dateObj = page.created ? dv.date(page.created) : page.file.mtime;
    if (dateObj && typeof dateObj.setZone === "function") {
      dateObj = dateObj.setZone(IST_ZONE);
    }
    if (!dateObj || typeof dateObj.toFormat !== "function") continue;

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
          content: "", // keep cells clean; no long text rendered inside blocks
        }))
        .sort((a, b) => a.date.localeCompare(b.date));

      const calendarData = {
        year,
        entries,
      };

      dv.header(4, `${year}`);
      const wrap = this.container.createDiv({ cls: "writing-heatmap-wrap" });
      const weekdayCol = wrap.createDiv({ cls: "writing-heatmap-weekdays" });
      ["Sun", "Mon", "Tue", "Wed", "Thu", "Fri", "Sat"].forEach((d) =>
        weekdayCol.createDiv({ text: d })
      );
      const calCol = wrap.createDiv();
      renderHeatmapCalendar(calCol, calendarData);
    }
  }
}
```
