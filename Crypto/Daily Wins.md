<center><div style="margin-bottom: 24px;">Don’t break the chain!</div></center>

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
let style = document.getElementById(tooltipStyleId);
if (!style) {
  style = document.createElement("style");
  style.id = tooltipStyleId;
  document.head.appendChild(style);
}
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
      transform: translateY(5px);
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
    .writing-inline-toast {
      position: sticky;
      top: 8px;
      z-index: 70;
      width: fit-content;
      margin: 0 auto 14px;
      padding: 8px 12px;
      border-radius: 16px;
      border: 1px solid var(--background-modifier-border);
      background: rgba(22, 24, 29, 0.96);
      color: #e8edf2;
      font-size: 12px;
      line-height: 1.3;
      box-shadow: 0 8px 24px rgba(0, 0, 0, 0.35);
      opacity: 0;
      transform: translateY(-6px);
      transition: opacity 0.25s ease, transform 0.25s ease;
      pointer-events: none;
    }
    .writing-inline-toast.show {
      opacity: 1;
      transform: translateY(0);
    }
    .writing-streak-summary {
      display: flex;
      flex-wrap: wrap;
      gap: 10px;
      margin: 0;
      align-items: center;
      justify-content: flex-end;
      padding-right: 2px;
    }
    .writing-year-row {
      margin: 0 0 8px;
    }
    .writing-year-row-with-streak {
      display: flex;
      align-items: flex-start;
      justify-content: space-between;
      gap: 10px;
    }
    .writing-heatmap-wrap {
      position: relative;
      margin: 0 0 10px;
    }
    .writing-year-overlay {
      position: absolute;
      top: -40px;
      left: 0;
      transform: none;
      margin-left: 0;
      z-index: 60;
      width: 104px;
      height: 32px;
      display: grid;
      place-items: center;
      padding: 0;
      text-align: center;
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
      font-size: 20px;
      line-height: 1;
      pointer-events: none;
    }
    .writing-year-overlay-text {
      display: block;
      transform: translateY(2px);
    }
    .writing-streak-chip {
      display: inline-grid;
      grid-auto-flow: column;
      align-items: center;
      justify-content: center;
      justify-items: center;
      column-gap: 6px;
      padding: 8px 14px;
      line-height: 1.1;
      text-align: center;
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
    .writing-streak-chip-text {
      display: block;
      transform: translateY(2px);
    }
    .writing-streak-chip::before {
      content: "";
      width: 20px;
      height: 20px;
      display: block;
      place-self: center;
      margin-right: 0;
      background-repeat: no-repeat;
      background-size: 100% 100%;
      background-position: center;
      background-image: url(\"data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 24 24'%3E%3Cpath fill='%2317d953' d='M12.7 1.2c.4 2.3-.4 3.8-1.5 5.2-1.1 1.4-2 2.8-1.1 4.6.8 1.5 2.5 2.1 4.2 1 1.6-1 2.2-2.7 2-4.8 2.6 2 4.7 5 4.7 8.5A8.9 8.9 0 0 1 12 24a8.9 8.9 0 0 1-9-8.3c0-3.8 2.1-7 5.3-9-.4 1.9.1 3.5 1.2 4.6 1.1 1.1 2.9 1.2 3.9 0 1.2-1.3 1.2-3.1 1.3-10.1z'/%3E%3Cpath fill='%2385ff2e' d='M12 8.7c.3 1.4-.3 2.2-.9 3-.6.7-1 1.4-.6 2.3.4.8 1.3 1.2 2.1.6.8-.5 1.2-1.3 1.1-2.4 1.5 1.1 2.6 2.8 2.6 4.8A4.4 4.4 0 0 1 12 21.3 4.4 4.4 0 0 1 7.7 17c0-2.2 1.2-4 3-5.1-.2 1 .1 1.8.6 2.3.5.5 1.4.6 2 .1.6-.7.7-1.6.7-5.6z'/%3E%3C/svg%3E\");
    }
    :where(.heatmap-calendar-box, .day, [data-date]) {
      transition: transform 0.16s ease, filter 0.16s ease;
      transform-origin: center;
      will-change: transform;
    }
    :where(.heatmap-calendar-box, .day, [data-date]).writing-cell-empty {
      border-radius: 0 !important;
      background:
        linear-gradient(
          180deg,
          rgba(255, 255, 255, 0.14) 0%,
          rgba(255, 255, 255, 0.04) 42%,
          rgba(14, 20, 30, 0.26) 100%
        ) !important;
      border: 1px solid color-mix(in srgb, #ffffff 24%, transparent) !important;
      box-shadow:
        inset 0 1px 0 rgba(255, 255, 255, 0.22),
        inset 0 -8px 14px rgba(8, 12, 18, 0.18),
        0 2px 6px rgba(0, 0, 0, 0.2) !important;
      backdrop-filter: blur(7px) saturate(1.12);
      -webkit-backdrop-filter: blur(7px) saturate(1.12);
    }
    :where(.heatmap-calendar-box, .day, [data-date]):hover {
      transform: translateY(-1px) scale(1.14);
      filter: saturate(1.08) brightness(1.03);
      z-index: 2;
    }
    :where(.heatmap-calendar-box, .day, [data-date]).writing-cell-empty:hover {
      background:
        linear-gradient(
          180deg,
          rgba(255, 255, 255, 0.2) 0%,
          rgba(255, 255, 255, 0.08) 45%,
          rgba(14, 20, 30, 0.24) 100%
        ) !important;
      border-color: color-mix(in srgb, #ffffff 36%, transparent) !important;
      box-shadow:
        inset 0 1px 0 rgba(255, 255, 255, 0.28),
        inset 0 -8px 14px rgba(8, 12, 18, 0.12),
        0 8px 18px rgba(0, 0, 0, 0.28) !important;
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
const showInlineToast = (message, durationMs = 5000) => {
  const host = this.container || dv.container;
  if (!host) return;
  let toast = host.querySelector(".writing-inline-toast");
  if (!toast) {
    toast = document.createElement("div");
    toast.className = "writing-inline-toast";
    host.prepend(toast);
  }
  toast.textContent = String(message || "");
  toast.classList.add("show");
  clearTimeout(toast._hideTimer);
  clearTimeout(toast._removeTimer);
  toast._hideTimer = setTimeout(() => {
    toast.classList.remove("show");
  }, Math.max(0, durationMs - 250));
  toast._removeTimer = setTimeout(() => {
    if (toast && toast.parentElement) toast.remove();
  }, durationMs);
};

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
  const historyNoticeKey = "writing-heatmap-save-notice-v1:Daily-Wins";
  const historyFilePath = "Crypto/.writing-heatmap-history.json";
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
    let shouldShowSaveNotice = true;
    try {
      shouldShowSaveNotice = sessionStorage.getItem(historyNoticeKey) !== "1";
    } catch {}
    if (shouldShowSaveNotice) {
      showInlineToast(`History backup updated: ${historyFilePath}`, 5000);
      try {
        sessionStorage.setItem(historyNoticeKey, "1");
      } catch {}
    }
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

      const yearRow = dv.el("div", "", { cls: "writing-year-row" });
      if (year === years[0]) {
        yearRow.classList.add("writing-year-row-with-streak");
        const streakEl = yearRow.createEl("div", { cls: "writing-streak-summary" });
        const currentChip = streakEl.createEl("span", { cls: "writing-streak-chip" });
        currentChip.createEl("span", {
          cls: "writing-streak-chip-text",
          text: `Current: ${currentStreak} day${currentStreak === 1 ? "" : "s"}`,
        });
        const bestChip = streakEl.createEl("span", { cls: "writing-streak-chip" });
        bestChip.createEl("span", {
          cls: "writing-streak-chip-text",
          text: `Best: ${maxStreak} day${maxStreak === 1 ? "" : "s"}`,
        });
      }
      const heatmapWrap = this.container.createDiv({ cls: "writing-heatmap-wrap" });
      const yearContainer = heatmapWrap.createDiv();
      renderHeatmapCalendar(yearContainer, calendarData);
      const yearOverlay = heatmapWrap.createEl("div", { cls: "writing-year-overlay" });
      yearOverlay.createEl("span", { cls: "writing-year-overlay-text", text: `${year}` });

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
          cell.classList.remove("writing-cell-marked", "writing-cell-empty");
          if (!date || !tooltipByDate.has(date)) {
            cell.classList.add("writing-cell-empty");
            continue;
          }
          cell.classList.add("writing-cell-marked");
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
  const IST_ZONE = "Asia/Kolkata";
  dv.el("div", "", { cls: "dw-page-anchor" });
  const isInDailyWinsFolder = (filePath) =>
    filePath.startsWith(`${targetFolderName}/`) ||
    filePath.includes(`/${targetFolderName}/`);

  const styleId = "daily-wins-folder-cards-style";
  if (!document.getElementById(styleId)) {
    const style = document.createElement("style");
    style.id = styleId;
    style.textContent = `
      .dw-page-anchor {
        display: none;
      }
      .markdown-reading-view:has(.dw-page-anchor) {
        background:
          radial-gradient(700px 420px at 6% 10%, color-mix(in srgb, var(--pg-tint-1, rgba(110, 72, 196, 0.15)) 100%, transparent), transparent 68%),
          radial-gradient(620px 360px at 92% 88%, color-mix(in srgb, var(--pg-tint-2, rgba(149, 119, 232, 0.10)) 100%, transparent), transparent 70%),
          radial-gradient(420px 240px at 50% 0%, color-mix(in srgb, var(--pg-tint-3, rgba(95, 51, 182, 0.08)) 100%, transparent), transparent 72%),
          linear-gradient(
            160deg,
            var(--pg-base-1, #11131a),
            var(--pg-base-2, #161a24)
          ) !important;
        overflow-x: hidden !important;
        scrollbar-width: none;
      }
      .markdown-reading-view:has(.dw-page-anchor) .markdown-preview-view {
        background: transparent !important;
        overflow-x: hidden !important;
        scrollbar-width: none;
      }
      .markdown-reading-view:has(.dw-page-anchor) .block-language-dataviewjs:has(.dw-board),
      .markdown-reading-view:has(.dw-page-anchor) .el-div:has(.dw-board) {
        overflow: visible !important;
        max-height: none !important;
        scrollbar-width: none;
      }
      .markdown-reading-view:has(.dw-page-anchor)::-webkit-scrollbar,
      .markdown-reading-view:has(.dw-page-anchor) .markdown-preview-view::-webkit-scrollbar,
      .markdown-reading-view:has(.dw-page-anchor) .block-language-dataviewjs:has(.dw-board)::-webkit-scrollbar,
      .markdown-reading-view:has(.dw-page-anchor) .el-div:has(.dw-board)::-webkit-scrollbar {
        width: 0;
        height: 0;
        display: none;
      }
      .dw-board {
        width: min(860px, 95%);
        margin: 8px auto 0;
        position: relative;
        isolation: isolate;
        padding: 14px;
        box-sizing: border-box;
        overflow: visible !important;
        border-radius: 18px;
        background:
          radial-gradient(82% 68% at 8% 8%, var(--pg-tint-1, rgba(110, 72, 196, 0.15)) 0%, transparent 72%),
          radial-gradient(76% 62% at 92% 92%, var(--pg-tint-2, rgba(149, 119, 232, 0.10)) 0%, transparent 74%),
          linear-gradient(
            145deg,
            color-mix(in srgb, var(--pg-base-1, #11131a) 90%, var(--background-primary) 10%),
            color-mix(in srgb, var(--pg-base-2, #161a24) 88%, var(--background-secondary) 12%)
          );
        border: 1px solid var(--pg-border, rgba(149, 119, 232, 0.22));
        box-shadow:
          0 12px 24px rgba(6, 10, 20, 0.24),
          inset 0 1px 0 rgba(255, 255, 255, 0.06);
      }
      .dw-board::before,
      .dw-board::after {
        content: "";
        position: absolute;
        z-index: -1;
        border-radius: 999px;
        filter: blur(26px);
        opacity: 0.45;
        pointer-events: none;
      }
      .dw-board::before {
        width: 190px;
        height: 190px;
        top: -50px;
        left: -40px;
        background: color-mix(in srgb, var(--pg-tint-1, rgba(110, 72, 196, 0.15)) 80%, transparent);
      }
      .dw-board::after {
        width: 170px;
        height: 170px;
        right: -30px;
        bottom: -45px;
        background: color-mix(in srgb, var(--pg-tint-2, rgba(149, 119, 232, 0.10)) 90%, transparent);
      }
      .dw-toolbar {
        display: grid;
        grid-template-columns: minmax(0, 1fr) auto auto auto;
        gap: 8px;
        margin-bottom: 12px;
        align-items: center;
      }
      .dw-search {
        width: 100%;
        min-width: 0;
        border: 1px solid var(--background-modifier-border);
        border-radius: 10px;
        background: var(--background-primary);
        color: var(--text-normal);
        padding: 8px 10px;
        font-size: 13px;
      }
      .dw-button {
        border: 1px solid var(--background-modifier-border);
        border-radius: 10px;
        background: linear-gradient(
          170deg,
          var(--pg-surface, rgba(24, 27, 40, 0.34)),
          var(--pg-surface-2, rgba(31, 35, 50, 0.28))
        );
        color: var(--text-normal);
        padding: 8px 10px;
        font-size: 12px;
        font-weight: 600;
        cursor: pointer;
      }
      .dw-button:hover {
        filter: brightness(1.06);
      }
      .dw-stats {
        display: flex;
        flex-wrap: wrap;
        gap: 8px;
        margin: 0 0 12px;
      }
      .dw-chip {
        border: 1px solid var(--background-modifier-border);
        border-radius: 999px;
        padding: 4px 10px;
        font-size: 12px;
        color: var(--text-muted);
        background: var(--background-secondary);
      }
      .dw-cards {
        display: flex;
        flex-direction: column;
        gap: 12px;
        min-width: 0;
        overflow: visible !important;
      }
      .dw-card {
        margin: 0;
        width: 100%;
        min-width: 0;
        border: 1px solid var(--background-modifier-border);
        border-radius: 14px;
        background: linear-gradient(
          165deg,
          var(--pg-surface, rgba(24, 27, 40, 0.34)),
          color-mix(in srgb, var(--pg-surface-2, rgba(31, 35, 50, 0.28)) 88%, var(--background-secondary) 12%)
        );
        box-shadow: 0 8px 20px rgba(0, 0, 0, 0.16);
        overflow: hidden;
        scrollbar-width: none;
      }
      .dw-card::-webkit-scrollbar {
        width: 0;
        height: 0;
        display: none;
      }
      .dw-card summary {
        list-style: none;
        cursor: pointer;
        font-weight: 800;
        padding: 11px 12px;
        border-bottom: 1px solid var(--background-modifier-border);
        user-select: none;
      }
      .dw-card summary::-webkit-details-marker {
        display: none;
      }
      .dw-summary-row {
        display: grid;
        grid-template-columns: minmax(0, 1fr) auto auto;
        align-items: center;
        gap: 8px;
      }
      .dw-summary-title {
        min-width: 0;
      }
      .dw-summary-count {
        font-size: 12px;
        color: var(--text-muted);
      }
      .dw-chevron {
        font-size: 13px;
        opacity: 0.8;
        transition: transform 0.16s ease;
      }
      .dw-card:not([open]) .dw-chevron {
        transform: rotate(-90deg);
      }
      .dw-meta {
        margin: 8px 12px 0;
        display: flex;
        gap: 8px;
        flex-wrap: wrap;
      }
      .dw-meta-chip {
        border: 1px solid var(--background-modifier-border);
        border-radius: 999px;
        padding: 3px 8px;
        font-size: 11px;
        color: var(--text-muted);
        background: color-mix(in srgb, var(--pg-surface-2, rgba(31, 35, 50, 0.28)) 94%, transparent);
      }
      .dw-meta-streak {
        color: color-mix(in srgb, var(--text-normal) 84%, #ff9f1a 16%);
      }
      .dw-day {
        margin: 8px 12px 2px;
        font-size: 11px;
        text-transform: uppercase;
        letter-spacing: 0.04em;
        color: var(--text-faint);
      }
      .dw-list {
        margin: 0;
        padding: 3px 12px 8px 22px;
      }
      .dw-list li {
        margin: 3px 0;
      }
      .dw-list-item {
        display: grid;
        grid-template-columns: 64px 76px minmax(0, 1fr);
        align-items: baseline;
        gap: 12px;
        padding-left: 8px;
      }
      .dw-list-item.dw-today {
        background: linear-gradient(
          90deg,
          color-mix(in srgb, #17d953 70%, var(--interactive-accent) 30%) 0 2px,
          transparent 2px
        );
        border-radius: 4px;
      }
      .dw-date {
        min-width: 44px;
        font-size: 11px;
        color: var(--text-faint);
        font-variant-numeric: tabular-nums;
      }
      .dw-time {
        min-width: 54px;
        font-size: 12px;
        color: var(--text-muted);
        font-variant-numeric: tabular-nums;
      }
      .dw-link {
        color: var(--text-normal);
        text-decoration: none;
        overflow-wrap: anywhere;
      }
      .dw-link:hover,
      .dw-link:focus-visible {
        color: var(--text-normal);
        text-decoration: underline;
      }
      .dw-empty {
        border: 1px dashed var(--background-modifier-border);
        border-radius: 12px;
        padding: 16px;
        text-align: center;
        color: var(--text-muted);
      }
      @media (max-width: 700px) {
        .dw-toolbar {
          grid-template-columns: 1fr 1fr;
        }
      }
    `;
    document.head.appendChild(style);
  }

  const notes = app.vault
    .getMarkdownFiles()
    .filter((f) => isInDailyWinsFolder(f.path))
    .filter((f) => String(f.basename || "").toLowerCase() !== "months")
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

  const parseMonthIndex = (name) => {
    const m = String(name || "").match(/^\s*(\d{1,2})/);
    return m ? Number(m[1]) : null;
  };
  const zonedParts = (ms, timeZone = IST_ZONE) => {
    const dtf = new Intl.DateTimeFormat("en-US", {
      timeZone,
      weekday: "short",
      year: "numeric",
      month: "2-digit",
      day: "2-digit",
      hour: "2-digit",
      minute: "2-digit",
      hour12: true,
    });
    const out = {};
    for (const p of dtf.formatToParts(new Date(ms || Date.now()))) {
      if (p.type !== "literal") out[p.type] = p.value;
    }
    return out;
  };
  const keyToSerial = (key) => {
    const [y, m, d] = String(key).split("-").map(Number);
    return Math.floor(Date.UTC(y, m - 1, d) / 86400000);
  };
  const dateDiffDays = (aKey, bKey) => keyToSerial(aKey) - keyToSerial(bKey);
  const formatDayKey = (ms) => {
    const p = zonedParts(ms);
    return `${p.year}-${p.month}-${p.day}`;
  };
  const weekdayLongByShort = {
    Mon: "Monday",
    Tue: "Tuesday",
    Wed: "Wednesday",
    Thu: "Thursday",
    Fri: "Friday",
    Sat: "Saturday",
    Sun: "Sunday",
  };
  const formatDayHeading = (ms) => {
    const p = zonedParts(ms);
    return `${weekdayLongByShort[p.weekday] || p.weekday}, ${p.month}/${p.day}`;
  };
  const formatDayLabel = (ms) => {
    const p = zonedParts(ms);
    return `${p.day} ${p.weekday}`;
  };
  const formatTimeLabel = (ms) => {
    const p = zonedParts(ms);
    return `${p.hour}:${p.minute} ${p.dayPeriod}`;
  };
  const startOfWeekKey = (() => {
    const todayKey = formatDayKey(Date.now());
    const todayWeekday = zonedParts(Date.now()).weekday;
    const dayOrder = { Mon: 0, Tue: 1, Wed: 2, Thu: 3, Fri: 4, Sat: 5, Sun: 6 };
    const daysFromMonday = dayOrder[todayWeekday] ?? 0;
    const startSerial = keyToSerial(todayKey) - daysFromMonday;
    const d = new Date(startSerial * 86400000);
    return `${d.getUTCFullYear()}-${String(d.getUTCMonth() + 1).padStart(2, "0")}-${String(
      d.getUTCDate()
    ).padStart(2, "0")}`;
  })();
  const todayKey = formatDayKey(Date.now());
  const streakFromDayKeys = (dayKeys) => {
    const sorted = [...new Set(dayKeys)]
      .sort((a, b) => keyToSerial(a) - keyToSerial(b));
    if (!sorted.length) return 0;
    let best = 1;
    let run = 1;
    for (let i = 1; i < sorted.length; i++) {
      if (dateDiffDays(sorted[i], sorted[i - 1]) === 1) {
        run += 1;
      } else {
        best = Math.max(best, run);
        run = 1;
      }
    }
    return Math.max(best, run);
  };

  const folders = [...byFolder.entries()]
    .map(([folderPath, { folderName, files }]) => {
      const displayName =
        String(folderName || "")
          .replace(/^\s*\d+\s*[\.\-_:)]\s*/u, "")
          .trim() || folderName;
      const enrichedFiles = files.map((f) => {
        const when = Number(f.stat?.ctime || f.stat?.mtime || Date.now());
        return {
          name: f.basename,
          path: f.path,
          when,
          dayKey: formatDayKey(when),
        };
      });
      const uniqueDays = new Set(enrichedFiles.map((f) => f.dayKey));
      const whenValues = enrichedFiles.map((f) => f.when);
      return {
        folderPath,
        folderName,
        displayName,
        monthIndex: parseMonthIndex(folderName),
        files: enrichedFiles,
        fileCount: enrichedFiles.length,
        dayCount: uniqueDays.size,
        bestStreak: streakFromDayKeys(enrichedFiles.map((f) => f.dayKey)),
        minWhen: Math.min(...whenValues),
        maxWhen: Math.max(...whenValues),
      };
    })
    .sort((a, b) => {
      if (a.monthIndex != null && b.monthIndex != null && a.monthIndex !== b.monthIndex) {
        return a.monthIndex - b.monthIndex;
      }
      if (a.minWhen !== b.minWhen) return a.minWhen - b.minWhen;
      return String(a.folderName).localeCompare(String(b.folderName));
    });

  const board = dv.el("div", "", { cls: "dw-board" });
  const toolbar = board.createEl("div", { cls: "dw-toolbar" });
  const searchInput = toolbar.createEl("input", {
    cls: "dw-search",
    type: "search",
    placeholder: "Search note titles...",
  });
  const sortButton = toolbar.createEl("button", { cls: "dw-button", text: "Oldest first" });
  const weekButton = toolbar.createEl("button", { cls: "dw-button", text: "This week : Off" });
  const expandButton = toolbar.createEl("button", { cls: "dw-button", text: "Expand all" });
  const statsRow = board.createEl("div", { cls: "dw-stats" });
  const cardsHost = board.createEl("div", { cls: "dw-cards" });

  let query = "";
  let newestFirst = false;
  let thisWeekOnly = false;
  let expandAll = false;
  const openState = new Map();

  const addChip = (text) => statsRow.createEl("span", { cls: "dw-chip", text });
  const byDay = (files) => {
    const groups = new Map();
    for (const file of files) {
      if (!groups.has(file.dayKey)) groups.set(file.dayKey, []);
      groups.get(file.dayKey).push(file);
    }
    return [...groups.entries()];
  };

  const render = () => {
    statsRow.innerHTML = "";
    cardsHost.innerHTML = "";
    sortButton.textContent = `Sort: ${newestFirst ? "Newest" : "Oldest"}`;
    weekButton.textContent = `This week : ${thisWeekOnly ? "On" : "Off"}`;
    expandButton.textContent = expandAll ? "Collapse all" : "Expand all";
    const hasActiveFilter = Boolean(query) || thisWeekOnly;

    const visibleFolders = folders
      .map((folder) => {
        const folderMatchesQuery =
          folder.displayName.toLowerCase().includes(query) ||
          String(folder.folderName || "").toLowerCase().includes(query);
        const filtered = folder.files.filter((f) => {
          const matchQuery =
            !query ||
            folderMatchesQuery ||
            f.name.toLowerCase().includes(query) ||
            f.path.toLowerCase().includes(query);
          if (!matchQuery) return false;
          if (!thisWeekOnly) return true;
          return keyToSerial(f.dayKey) >= keyToSerial(startOfWeekKey);
        });
        return { ...folder, visibleFiles: filtered };
      })
      .filter((folder) => folder.visibleFiles.length > 0);

    const totalVisibleFiles = visibleFolders.reduce((acc, folder) => acc + folder.visibleFiles.length, 0);
    const totalVisibleDays = new Set(
      visibleFolders.flatMap((folder) => folder.visibleFiles.map((f) => `${folder.folderPath}:${f.dayKey}`))
    ).size;
    addChip(`${visibleFolders.length} month${visibleFolders.length === 1 ? "" : "s"}`);
    addChip(`${totalVisibleFiles} file${totalVisibleFiles === 1 ? "" : "s"}`);
    addChip(`${totalVisibleDays} active day${totalVisibleDays === 1 ? "" : "s"}`);

    if (!visibleFolders.length) {
      cardsHost.createEl("div", { cls: "dw-empty", text: "No files match your search." });
      return;
    }

    for (const folder of visibleFolders) {
      const orderedFiles = folder.visibleFiles.slice().sort((a, b) => {
        if (a.when !== b.when) return newestFirst ? b.when - a.when : a.when - b.when;
        return a.name.localeCompare(b.name);
      });
      const visibleDayCount = new Set(orderedFiles.map((f) => f.dayKey)).size;
      const visibleStreak = streakFromDayKeys(orderedFiles.map((f) => f.dayKey));

      const details = cardsHost.createEl("details", { cls: "dw-card" });
      const rememberedOpen = openState.get(folder.folderPath) === true;
      details.open = expandAll || hasActiveFilter || rememberedOpen;
      details.addEventListener("toggle", () => {
        openState.set(folder.folderPath, details.open);
      });

      const summary = details.createEl("summary");
      const row = summary.createEl("div", { cls: "dw-summary-row" });
      row.createEl("span", { cls: "dw-summary-title", text: folder.displayName });
      row.createEl("span", {
        cls: "dw-summary-count",
        text: `${orderedFiles.length}/${folder.fileCount} files`,
      });
      row.createEl("span", { cls: "dw-chevron", text: "▾" });

      const meta = details.createEl("div", { cls: "dw-meta" });
      meta.createEl("span", {
        cls: "dw-meta-chip",
        text:
          visibleDayCount === folder.dayCount
            ? `${visibleDayCount} active days`
            : `${visibleDayCount}/${folder.dayCount} active days`,
      });
      meta.createEl("span", {
        cls: "dw-meta-chip dw-meta-streak",
        text:
          visibleStreak === folder.bestStreak
            ? `Best streak ${visibleStreak}d`
            : `Best streak ${visibleStreak}/${folder.bestStreak}d`,
      });

      for (const [, files] of byDay(orderedFiles)) {
        const dayWhen = files[0]?.when || Date.now();
        details.createEl("div", { cls: "dw-day", text: formatDayHeading(dayWhen) });
        const list = details.createEl("ul", { cls: "dw-list" });
        for (const file of files) {
          const li = list.createEl("li", {
            cls: `dw-list-item${file.dayKey === todayKey ? " dw-today" : ""}`,
          });
          li.createEl("span", { cls: "dw-date", text: formatDayLabel(file.when) });
          li.createEl("span", { cls: "dw-time", text: formatTimeLabel(file.when) });
          const a = li.createEl("a", { text: file.name, href: "#", cls: "dw-link" });
          a.addEventListener("click", (event) => {
            event.preventDefault();
            app.workspace.openLinkText(file.path, "", false);
          });
        }
      }
    }
  };

  searchInput.addEventListener("input", () => {
    query = String(searchInput.value || "").trim().toLowerCase();
    render();
  });
  sortButton.addEventListener("click", () => {
    newestFirst = !newestFirst;
    render();
  });
  weekButton.addEventListener("click", () => {
    thisWeekOnly = !thisWeekOnly;
    render();
  });
  expandButton.addEventListener("click", () => {
    expandAll = !expandAll;
    for (const folder of folders) openState.set(folder.folderPath, expandAll);
    render();
  });

  render();
})();
```

