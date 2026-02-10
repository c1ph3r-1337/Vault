<center>🔗 Don’t break the chain! 🔗</center>

```dataviewjs
// 1) Include all notes in this vault section (Crypto folder and subfolders).
// Dataview already scopes to markdown pages, so this captures files created anywhere here.
const IST_ZONE = "Asia/Kolkata";
const escapeHtml = (s) =>
  String(s)
    .replaceAll("&", "&amp;")
    .replaceAll("<", "&lt;")
    .replaceAll(">", "&gt;")
    .replaceAll('"', "&quot;")
    .replaceAll("'", "&#39;");

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
    .writing-heatmap-tooltip-title {
      font-weight: 700;
      margin-bottom: 6px;
    }
    .writing-heatmap-tooltip-link {
      display: block;
      color: var(--text-normal);
      text-decoration: none;
      border-radius: 6px;
      padding: 2px 4px;
      transition: background 0.12s ease, box-shadow 0.12s ease;
    }
    .writing-heatmap-tooltip-link:hover {
      background: var(--background-modifier-hover);
      color: var(--text-normal);
      text-decoration: none;
      outline: none;
    }
    .writing-heatmap-tooltip-link:focus,
    .writing-heatmap-tooltip-link:focus-visible {
      outline: none;
      box-shadow: 0 0 0 1px var(--background-modifier-border-focus) inset;
      background: var(--background-modifier-hover);
      color: var(--text-normal);
    }
    .writing-heatmap-tooltip-link:active {
      background: var(--background-modifier-active-hover);
      color: var(--text-normal);
    }
    :where(.heatmap-calendar-box, .day, [data-date]) {
      transition: transform 0.16s ease, filter 0.16s ease;
      transform-origin: center;
      will-change: transform;
    }
    :where(.heatmap-calendar-box, .day, [data-date]):hover {
      transform: translateY(-1px) scale(1.14);
      filter: saturate(1.08) brightness(1.03);
      z-index: 2;
    }
    @media (prefers-reduced-motion: reduce) {
      :where(.heatmap-calendar-box, .day, [data-date]) {
        transition: none;
      }
      :where(.heatmap-calendar-box, .day, [data-date]):hover {
        transform: none;
        filter: none;
      }
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
const closeTooltipNow = (suppressMs = 0) => {
  tooltipEl._writingPinnedCell = null;
  tooltipEl._writingSuppressUntil = Math.max(
    Number(tooltipEl._writingSuppressUntil || 0),
    Date.now() + suppressMs
  );
  tooltipEl.classList.remove("show");
  tooltipEl.style.pointerEvents = "none";
  tooltipEl.innerHTML = "";
};
if (!tooltipEl.dataset.linkHandlerBound) {
  tooltipEl.dataset.linkHandlerBound = "1";
  tooltipEl.addEventListener("click", (event) => {
    const link = event.target.closest(".writing-heatmap-tooltip-link");
    if (!link) return;
    event.preventDefault();
    event.stopPropagation();
    const path = link.dataset.path;
    closeTooltipNow(300);
    if (path) app.workspace.openLinkText(path, "", false);
  });
}
if (!tooltipEl.dataset.globalResetBound) {
  tooltipEl.dataset.globalResetBound = "1";
  const resetTooltipState = () => {
    closeTooltipNow();
  };
  window.addEventListener("blur", resetTooltipState);
  document.addEventListener("visibilitychange", () => {
    if (document.hidden) resetTooltipState();
  });
}

// 2) Scan all pages in this area so file activity anywhere in Crypto updates the heatmap.
const pages = dv.pages();

// DataviewJS runs in an eval context, so avoid top-level "return".
if (pages.length === 0) {
  dv.paragraph("No notes found to plot on the heatmap.");
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
    day.files.push({ name: page.file.name, path: page.file.path });
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
          `${date}\n${value.files.map((f) => `- ${f.name}`).join("\n")}`,
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
        const getPinnedCell = () => tooltipEl._writingPinnedCell || null;
        const tooltipSuppressed = () => Date.now() < Number(tooltipEl._writingSuppressUntil || 0);
        const setPinnedCell = (cell) => {
          tooltipEl._writingPinnedCell = cell || null;
        };
        const moveTooltip = (event) => {
          tooltipEl.style.left = `${event.clientX + 12}px`;
          tooltipEl.style.top = `${event.clientY + 12}px`;
        };
        const hideTooltip = () => {
          tooltipEl.classList.remove("show");
          tooltipEl.style.pointerEvents = "none";
        };
        const showHoverTooltip = (cell, event) => {
          if (tooltipSuppressed()) return;
          tooltipEl.textContent = cell.dataset.writingTooltip || "";
          tooltipEl.style.pointerEvents = "none";
          moveTooltip(event);
          tooltipEl.classList.add("show");
        };
        const showPinnedTooltip = (date, event) => {
          if (tooltipSuppressed()) return;
          const files = dayMap.get(date)?.files || [];
          const weekday = window.moment(date, "YYYY-MM-DD").format("ddd");
          const title = `${weekday}, ${date}`;
          const links = files
            .map(
              (f) =>
                `<a href="#" class="writing-heatmap-tooltip-link" data-path="${escapeHtml(
                  f.path
                )}">${escapeHtml(f.name)}</a>`
            )
            .join("");
          tooltipEl.innerHTML = `<div class="writing-heatmap-tooltip-title">${escapeHtml(
            title
          )}</div>${links || "<div>No files</div>"}`;
          tooltipEl.style.pointerEvents = "auto";
          moveTooltip(event);
          tooltipEl.classList.add("show");
        };
        if (!tooltipEl.dataset.pinnedCloseBound) {
          tooltipEl.dataset.pinnedCloseBound = "1";
          tooltipEl.addEventListener("mouseleave", () => {
            setPinnedCell(null);
            hideTooltip();
          });
        }
        for (const cell of cells) {
          const date =
            cell.getAttribute("data-date") ||
            cell.dataset?.date ||
            cell.getAttribute("date");
          if (!date || !tooltipByDate.has(date)) continue;
          cell.removeAttribute("title");
          cell.dataset.writingTooltip = tooltipByDate.get(date);
          cell.addEventListener("mouseenter", (event) => {
            if (getPinnedCell()) return;
            showHoverTooltip(cell, event);
          });
          cell.addEventListener("mousemove", (event) => {
            if (getPinnedCell()) return;
            moveTooltip(event);
          });
          cell.addEventListener("mouseleave", () => {
            if (getPinnedCell()) return;
            hideTooltip();
          });
          cell.addEventListener("dblclick", (event) => {
            event.preventDefault();
            event.stopPropagation();
            if (getPinnedCell() === cell) {
              setPinnedCell(null);
              hideTooltip();
              return;
            }
            setPinnedCell(cell);
            showPinnedTooltip(date, event);
          });
          if (cell.childElementCount === 0) cell.textContent = "";
        }
      }, 0);
    }
  }
}
```

[[]]