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
    .writing-streak-summary {
      display: inline-flex;
      flex-wrap: wrap;
      gap: 8px;
      margin: 0 0 8px;
      align-items: center;
    }
    .writing-streak-chip {
      display: inline-flex;
      align-items: center;
      padding: 6px 12px;
      border-radius: 999px;
      border: 1px solid var(--background-modifier-border);
      background: linear-gradient(
        135deg,
        color-mix(in srgb, var(--background-primary) 86%, var(--interactive-accent) 14%),
        var(--background-secondary)
      );
      box-shadow: 0 6px 14px rgba(0, 0, 0, 0.2);
      color: var(--text-normal);
      font-weight: 700;
      font-size: 12px;
    }
    .writing-streak-chip::before {
      content: "";
      width: 14px;
      height: 14px;
      margin-right: 6px;
      background-repeat: no-repeat;
      background-size: contain;
      background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 64 64'%3E%3Cpath fill='%23ff7a00' d='M42 6c2 9-3 13-7 18-4 5-6 9-2 14 3 3 8 2 11-2 4-5 5-10 4-16 10 8 16 19 16 30 0 17-14 31-32 31S0 67 0 50c0-12 7-23 18-31-2 8 1 14 6 18 4 4 10 5 14 0 4-5 4-11 4-31z'/%3E%3Cpath fill='%23ffd54a' d='M32 33c1 6-2 8-4 11-3 3-4 5-2 8s5 3 8 1c3-3 4-6 4-10 5 5 8 10 8 16 0 9-7 16-16 16s-16-7-16-16c0-6 3-12 9-16-1 4 0 7 2 9 3 2 6 2 8-1 2-2 2-6 2-18z'/%3E%3C/svg%3E");
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

// 2) Scan only Crypto files and keep per-file day history so past edited days remain visible.
const CRYPTO_ROOT = "Crypto";
const isInCrypto = (filePath) => {
  const p = String(filePath || "");
  // In this vault, file paths are root-relative (e.g. "Daily Wins/...")
  // so everything here is already inside Crypto.
  if (!p) return false;
  return true;
};
const pages = dv.pages().where((p) => isInCrypto(p.file?.path));

// DataviewJS runs in an eval context, so avoid top-level "return".
if (pages.length === 0) {
  dv.paragraph("No notes found to plot on the heatmap.");
} else {
  // 3) Group notes by year/day.
  // Persist a simple per-file day history so editing a file on a new day
  // adds that day without removing older days already seen.
  const byYear = new Map(); // year -> Map(dateKey -> {count, files[]})
  const historyKey = "writing-heatmap-history-v2:Crypto";
  const historyBootstrapKey = "writing-heatmap-bootstrap-v1:Crypto";
  const historyFilePath = "Vault/Crypto/.writing-heatmap-history.json";
  let historySaveError = null;
  let historySaveOk = false;
  const loadHistoryByPath = async () => {
    try {
      const exists = await app.vault.adapter.exists(historyFilePath);
      if (!exists) return null;
      const raw = await app.vault.adapter.read(historyFilePath);
      const parsed = JSON.parse(raw || "{}");
      return parsed && typeof parsed === "object" ? parsed : {};
    } catch {
      return null;
    }
  };
  const saveHistoryByPath = async (history) => {
    historySaveOk = false;
    try {
      const payload = JSON.stringify(history, null, 2);
      const existing = app.vault.getAbstractFileByPath(historyFilePath);
      if (existing) {
        await app.vault.modify(existing, payload);
      } else {
        await app.vault.create(historyFilePath, payload);
      }
      historySaveError = null;
      historySaveOk = true;
    } catch (err) {
      try {
        const payload = JSON.stringify(history, null, 2);
        await app.vault.adapter.write(historyFilePath, payload);
        historySaveError = null;
        historySaveOk = true;
      } catch (err2) {
        historySaveError = String(
          err2?.message || err2 || err?.message || err || "Unknown save error"
        );
      }
    }
  };
  const loadLocalHistory = () => {
    try {
      return JSON.parse(localStorage.getItem(historyKey) || "{}");
    } catch {
      return {};
    }
  };
  const saveLocalHistory = (history) => {
    try {
      localStorage.setItem(historyKey, JSON.stringify(history));
    } catch {}
  };
  const mergeHistory = (base, incoming) => {
    const out = { ...(base || {}) };
    for (const [path, dates] of Object.entries(incoming || {})) {
      const merged = new Set(Array.isArray(out[path]) ? out[path] : []);
      for (const d of Array.isArray(dates) ? dates : []) merged.add(d);
      out[path] = [...merged].sort();
    }
    return out;
  };

  // Primary runtime source: localStorage.
  // One-time per app session: bootstrap/repair localStorage from JSON backup.
  let historyByPath = loadLocalHistory();
  let shouldBootstrap = true;
  try {
    shouldBootstrap = sessionStorage.getItem(historyBootstrapKey) !== "1";
  } catch {}
  if (shouldBootstrap) {
    const jsonHistory = await loadHistoryByPath();
    if (jsonHistory && typeof jsonHistory === "object") {
      historyByPath = mergeHistory(historyByPath, jsonHistory);
      saveLocalHistory(historyByPath);
    }
    try {
      sessionStorage.setItem(historyBootstrapKey, "1");
    } catch {}
  }

  const toDateKey = (value) => {
    if (!value) return null;
    let dt = value;
    if (typeof dt.setZone === "function") dt = dt.setZone(IST_ZONE);
    if (!dt || typeof dt.toFormat !== "function") return null;
    return dt.toFormat("yyyy-MM-dd");
  };

  for (const page of pages) {
    const path = page.file?.path;
    if (!path) continue;

    const createdKey = toDateKey(page.created ? dv.date(page.created) : page.file?.ctime);
    const modifiedKey = toDateKey(page.file?.mtime);

    const dateSet = new Set(Array.isArray(historyByPath[path]) ? historyByPath[path] : []);
    if (createdKey) dateSet.add(createdKey);
    if (modifiedKey) dateSet.add(modifiedKey);
    historyByPath[path] = [...dateSet].sort();

    for (const dateKey of dateSet) {
      const year = Number(dateKey.slice(0, 4));
      if (!Number.isFinite(year)) continue;

      if (!byYear.has(year)) byYear.set(year, new Map());
      const dayMap = byYear.get(year);

      if (!dayMap.has(dateKey)) {
        dayMap.set(dateKey, { count: 0, files: [], filePaths: new Set() });
      }
      const day = dayMap.get(dateKey);
      if (day.filePaths.has(path)) continue;

      day.filePaths.add(path);
      day.count += 1;
      day.files.push({ name: page.file.name, path });
    }
  }
  try {
    await saveHistoryByPath(historyByPath);
  } catch {}
  saveLocalHistory(historyByPath);
  if (historySaveError) {
    dv.paragraph(`History save error: ${historySaveError}`);
  } else if (historySaveOk) {
    dv.paragraph(`History backup updated: ${historyFilePath}`);
  }

  if (byYear.size === 0) {
    dv.paragraph("No valid dates found to plot on the heatmap.");
  } else {
    const allDates = [...new Set(
      [...byYear.values()].flatMap((dayMap) => [...dayMap.keys()])
    )].sort((a, b) => a.localeCompare(b));
    let maxStreak = 0;
    let currentStreak = 0;
    let run = 0;
    let prevDate = null;
    for (const dateKey of allDates) {
      const isConsecutive =
        prevDate &&
        window.moment(dateKey, "YYYY-MM-DD").diff(
          window.moment(prevDate, "YYYY-MM-DD"),
          "days"
        ) === 1;
      run = isConsecutive ? run + 1 : 1;
      if (run > maxStreak) maxStreak = run;
      prevDate = dateKey;
    }
    currentStreak = run;

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
      if (year === years[0]) {
        const streakEl = dv.el("div", "", { cls: "writing-streak-summary" });
        streakEl.createEl("span", {
          cls: "writing-streak-chip",
          text: `Current: ${currentStreak} day${currentStreak === 1 ? "" : "s"}`,
        });
        streakEl.createEl("span", {
          cls: "writing-streak-chip",
          text: `Best: ${maxStreak} day${maxStreak === 1 ? "" : "s"}`,
        });
      }
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



```dataviewjs
(() => {
  const targetFolderName = "Daily Wins";
  const isInDailyWinsFolder = (filePath) =>
    filePath.startsWith(`${targetFolderName}/`) ||
    filePath.includes(`/${targetFolderName}/`);

  const styleId = "daily-wins-folder-cards-style";
  if (!document.getElementById(styleId)) {
    const style = document.createElement("style");
    style.id = styleId;
    style.textContent = `
      .dw-cards {
        display: flex;
        flex-direction: column;
        gap: 10px;
        margin-top: 8px;
        align-items: center;
      }
      .dw-card {
        margin: 0;
        width: min(680px, 92%);
        border: 1px solid var(--background-modifier-border);
        border-radius: 12px;
        background: linear-gradient(
          160deg,
          color-mix(in srgb, var(--background-primary) 90%, var(--background-modifier-hover) 10%),
          var(--background-secondary)
        );
        box-shadow: 0 8px 20px rgba(0, 0, 0, 0.16);
        overflow: hidden;
      }
      .dw-card summary {
        list-style: none;
        cursor: pointer;
        font-weight: 700;
        padding: 10px 12px;
        border-bottom: 1px solid var(--background-modifier-border);
        user-select: none;
      }
      .dw-card summary::-webkit-details-marker {
        display: none;
      }
      .dw-card summary::after {
        content: "▾";
        float: right;
        opacity: 0.8;
      }
      .dw-card:not([open]) summary::after {
        content: "▸";
      }
      .dw-list {
        margin: 0;
        padding: 8px 12px 10px 22px;
      }
      .dw-list li {
        margin: 3px 0;
      }
      .dw-link {
        color: #ffffff;
        text-decoration: none;
      }
      .dw-link:hover,
      .dw-link:focus-visible {
        color: #ffffff;
        text-decoration: underline;
      }
    `;
    document.head.appendChild(style);
  }

  const notes = app.vault
    .getMarkdownFiles()
    .filter((f) => isInDailyWinsFolder(f.path))
    .sort((a, b) => a.path.localeCompare(b.path));

  if (!notes.length) {
    dv.paragraph(`No .md files found in any "${targetFolderName}" folder.`);
    return;
  }

  const byFolder = new Map();
  for (const note of notes) {
    const folderPath = note.parent?.path || "(root)";
    const folderName = note.parent?.name || folderPath;
    if (!byFolder.has(folderPath)) byFolder.set(folderPath, { folderName, files: [] });
    byFolder.get(folderPath).files.push(note);
  }

  const host = dv.el("div", "", { cls: "dw-cards" });
  for (const folderPath of [...byFolder.keys()].sort((a, b) => a.localeCompare(b))) {
    const { folderName, files } = byFolder.get(folderPath);
    const details = host.createEl("details", { cls: "dw-card" });
    details.createEl("summary", { text: folderName });
    const list = details.createEl("ul", { cls: "dw-list" });
    for (const f of files) {
      const li = list.createEl("li");
      const a = li.createEl("a", { text: f.basename, href: "#", cls: "dw-link" });
      a.addEventListener("click", (event) => {
        event.preventDefault();
        app.workspace.openLinkText(f.path, "", false);
      });
    }
  }
})();
```
