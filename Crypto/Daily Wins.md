<center>🔗 Don’t break the chain! 🔗</center>

```dataviewjs
// 1) Folder matcher:
// supports "Daily Wins", "Crypto/Daily Wins", "Vault/Crypto/Daily Wins", etc.
const isDailyWinsFolder = (folder) =>
  folder === "Daily Wins" || folder.endsWith("/Daily Wins");
const IST_ZONE = "Asia/Kolkata";

// Custom tooltip: rounded corners, no white border.
const tooltipStyleId = "writing-heatmap-tooltip-style";
if (!document.getElementById(tooltipStyleId)) {
  const style = document.createElement("style");
  style.id = tooltipStyleId;
  style.textContent = `
    .writing-heatmap-tooltip {
      position: fixed;
      z-index: 9999;
      max-width: 300px;
      padding: 8px 10px;
      border: none;
      border-radius: 10px;
      background: rgba(22, 24, 29, 0.96);
      color: #e8edf2;
      font-size: 12px;
      line-height: 1.35;
      white-space: pre-line;
      box-shadow: 0 8px 24px rgba(0, 0, 0, 0.35);
      pointer-events: none;
      opacity: 0;
      transform: translateY(2px);
      transition: opacity 0.12s ease, transform 0.12s ease;
    }
    .writing-heatmap-tooltip.show {
      opacity: 1;
      transform: translateY(0);
    }
  `;
  document.head.appendChild(style);
}

const tooltipId = "writing-heatmap-tooltip";
let tooltipEl = document.getElementById(tooltipId);
if (!tooltipEl) {
  tooltipEl = document.createElement("div");
  tooltipEl.id = tooltipId;
  tooltipEl.className = "writing-heatmap-tooltip";
  document.body.appendChild(tooltipEl);
}

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
  const byYear = new Map(); // year -> Map(dateKey -> {count, files[]})

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

    if (!dayMap.has(dateKey)) dayMap.set(dateKey, { count: 0, files: [] });
    const day = dayMap.get(dateKey);

    day.count += 1;
    day.files.push(page.file.name);
  }

  if (byYear.size === 0) {
    dv.paragraph("No valid dates found to plot on the heatmap.");
  } else {
    // 4) Build Heatmap Calendar data structure and render.
    // We render one heatmap per year so all notes are represented.
    const years = [...byYear.keys()].sort((a, b) => b - a);

    for (const year of years) {
      const dayMap = byYear.get(year);
      const tooltipByDate = new Map(
        [...dayMap.entries()].map(([date, value]) => [
          date,
          `${date}\n${value.files.map((f) => `- ${f}`).join("\n")}`,
        ])
      );

      const entries = [...dayMap.entries()]
        .map(([date, value]) => ({
          date, // required by Heatmap Calendar
          intensity: value.count, // higher value = stronger color
          content: "",
        }))
        .sort((a, b) => a.date.localeCompare(b.date));

      const calendarData = {
        year,
        entries,
      };

      dv.header(4, `${year}`);
      const yearContainer = this.container.createDiv();
      renderHeatmapCalendar(yearContainer, calendarData);

      // Add hover tooltip with all files written on each date.
      setTimeout(() => {
        const cells = yearContainer.querySelectorAll("[data-date], .heatmap-calendar-box, .day");
        const moveTooltip = (event) => {
          tooltipEl.style.left = `${event.clientX + 12}px`;
          tooltipEl.style.top = `${event.clientY + 12}px`;
        };
        for (const cell of cells) {
          const date =
            cell.getAttribute("data-date") ||
            cell.dataset?.date ||
            cell.getAttribute("date");
          if (!date || !tooltipByDate.has(date)) continue;
          cell.removeAttribute("title");
          cell.dataset.writingTooltip = tooltipByDate.get(date);
          cell.addEventListener("mouseenter", (event) => {
            tooltipEl.textContent = cell.dataset.writingTooltip || "";
            moveTooltip(event);
            tooltipEl.classList.add("show");
          });
          cell.addEventListener("mousemove", moveTooltip);
          cell.addEventListener("mouseleave", () => {
            tooltipEl.classList.remove("show");
          });
          if (cell.childElementCount === 0) cell.textContent = "";
        }
      }, 0);
    }
  }
}
```
