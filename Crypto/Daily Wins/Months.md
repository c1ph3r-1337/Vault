# Months Calendar Database

<center><div style="margin-bottom: 14px; font-weight: 700; letter-spacing: 0.03em;">Don’t break the chain!</div></center>

<div class="months-anchor"></div>

```dataviewjs
const styleId = "months-single-calendar-style-v1";
if (!document.getElementById(styleId)) {
  const style = document.createElement("style");
  style.id = styleId;
  style.textContent = `
    .markdown-reading-view:has(.months-anchor) h1 {
      text-align: center;
      margin-left: auto;
      margin-right: auto;
    }
    .markdown-reading-view:has(.months-anchor) .markdown-preview-view {
      scrollbar-width: none;
      -ms-overflow-style: none;
    }
    .markdown-reading-view:has(.months-anchor) .markdown-preview-view::-webkit-scrollbar {
      display: none;
      width: 0;
      height: 0;
    }
    .months-cal-wrap {
      width: min(860px, 96%);
      margin: 8px auto;
      border: 1px solid #3b4655;
      border-radius: 16px;
      padding: 12px;
      background:
        radial-gradient(75% 55% at 8% 8%, color-mix(in srgb, var(--interactive-accent) 18%, transparent) 0%, transparent 72%),
        linear-gradient(160deg, #151b22, #1c2430);
      box-shadow: 0 12px 24px rgba(0, 0, 0, 0.3);
      color: #d7dfea;
    }
    .months-header {
      position: relative;
      display: flex;
      align-items: center;
      justify-content: center;
      min-height: 56px;
      margin: 0 0 6px;
    }
    .months-title-nav {
      position: absolute;
      inset: 0;
      pointer-events: none;
    }
    .months-btn {
      position: absolute;
      top: 50%;
      transform: translateY(-50%);
      border: none;
      background: transparent;
      color: color-mix(in srgb, #ffffff 78%, var(--interactive-accent) 22%);
      font-size: 30px;
      font-weight: 900;
      padding: 0;
      height: 34px;
      width: 34px;
      box-sizing: border-box;
      cursor: pointer;
      display: inline-flex;
      align-items: center;
      justify-content: center;
      pointer-events: auto;
      line-height: 1;
      text-shadow: 0 2px 10px rgba(0, 0, 0, 0.55);
      transition: transform 0.14s ease, color 0.14s ease, filter 0.14s ease;
    }
    .months-btn-left {
      left: 4px;
    }
    .months-btn-right {
      right: 4px;
    }
    .months-btn:hover {
      color: color-mix(in srgb, #ffffff 52%, var(--interactive-accent) 48%);
      transform: translateY(-50%) scale(1.14);
      filter: drop-shadow(0 0 8px color-mix(in srgb, var(--interactive-accent) 55%, transparent));
    }
    .months-btn:active {
      transform: translateY(-50%) scale(1.02);
    }
    .months-btn:focus-visible {
      outline: none;
      color: color-mix(in srgb, #ffffff 40%, var(--interactive-accent) 60%);
      filter: drop-shadow(0 0 10px color-mix(in srgb, var(--interactive-accent) 60%, transparent));
    }
    .months-title {
      text-align: center;
      margin: 0;
      font-size: 2rem;
      line-height: 1;
      font-weight: 900;
      letter-spacing: 0.04em;
      color: color-mix(in srgb, var(--interactive-accent) 88%, #2ad46d);
      display: flex;
      align-items: center;
      justify-content: center;
      transform: none;
    }
    .months-grid {
      width: 100%;
      border-collapse: separate;
      border-spacing: 4px;
      table-layout: fixed;
    }
    .months-grid th,
    .months-grid td {
      border: 1px solid #4a5666;
      border-radius: 4px;
      background: #232c38;
      text-align: center;
      padding: 0;
      vertical-align: middle;
    }
    .months-grid thead th {
      height: 28px;
      font-size: 0.82rem;
      color: #cfd8e3;
      background: #2b3644;
      font-weight: 800;
    }
    .months-row-label {
      width: 92px;
      height: 82px;
      font-size: 2rem;
      font-weight: 900;
      letter-spacing: 0.01em;
      color: #d9e2ed;
      background: #2a3441 !important;
      line-height: 82px;
    }
    .months-day-cell {
      height: 82px;
      width: 82px;
      background: #252f3b !important;
      position: relative;
    }
    .months-day {
      display: block;
      font-size: 3.1rem;
      line-height: 1;
      font-weight: 900;
      margin: 0;
      line-height: 82px;
      color: #d7dee8;
    }
    .months-day-worked .months-day,
    .months-row-sun .months-day {
      color: color-mix(in srgb, var(--interactive-accent) 90%, #20c060);
    }
    .months-day-worked {
      background: color-mix(in srgb, var(--interactive-accent) 22%, #273340) !important;
      box-shadow: inset 0 0 0 1px color-mix(in srgb, var(--interactive-accent) 55%, transparent);
    }
    .months-empty {
      background: #202936 !important;
      opacity: 0.45;
    }
    .months-tip {
      margin-top: 8px;
      text-align: center;
      font-size: 12px;
      color: #9ca9b8;
    }
    .months-tooltip {
      position: fixed;
      z-index: 9999;
      max-width: 280px;
      border: 1px solid #4b5a6c;
      border-radius: 10px;
      background: rgba(16, 20, 26, 0.97);
      color: #e6edf5;
      padding: 8px 10px;
      box-shadow: 0 12px 24px rgba(0, 0, 0, 0.35);
      font-size: 12px;
      line-height: 1.35;
      pointer-events: none;
      opacity: 0;
      transform: translateY(4px);
      transition: opacity 0.12s ease, transform 0.12s ease;
      white-space: pre-line;
    }
    .months-tooltip-title {
      font-weight: 800;
      margin-bottom: 6px;
    }
    .months-tooltip-link {
      display: block;
      color: #ffffff;
      text-decoration: none;
      border-radius: 6px;
      padding: 2px 4px;
      white-space: normal;
    }
    .months-tooltip-link:hover,
    .months-tooltip-link:focus-visible {
      background: #2c3744;
      text-decoration: none;
      outline: none;
      color: #ffffff;
    }
    .months-tooltip.show {
      opacity: 1;
      transform: translateY(0);
    }
    @media (max-width: 860px) {
      .months-header {
        min-height: 48px;
        margin-top: 0;
      }
      .months-title {
        font-size: 1.6rem;
      }
      .months-title-nav {
        inset: 0;
      }
      .months-btn {
        width: 28px;
        height: 28px;
        font-size: 24px;
      }
      .months-row-label {
        width: 58px;
        height: 58px;
        font-size: 1.5rem;
        line-height: 58px;
      }
      .months-day-cell {
        height: 58px;
        width: 58px;
      }
      .months-day {
        font-size: 2rem;
        line-height: 58px;
      }
    }
  `;
  document.head.appendChild(style);
}

const tooltipId = "months-cal-tooltip-v1";
let tooltip = document.getElementById(tooltipId);
if (!tooltip) {
  tooltip = document.createElement("div");
  tooltip.id = tooltipId;
  tooltip.className = "months-tooltip";
  document.body.appendChild(tooltip);
}

const folderLeaf = (p) => {
  const parts = String(p || "").split("/").filter(Boolean);
  return parts.length ? parts[parts.length - 1] : String(p || "");
};
const escapeHtml = (s) => String(s)
  .replaceAll("&", "&amp;")
  .replaceAll("<", "&lt;")
  .replaceAll(">", "&gt;")
  .replaceAll('"', "&quot;")
  .replaceAll("'", "&#39;");

const monthNameMap = {
  JAN: 1, JANUARY: 1,
  FEB: 2, FEBRUARY: 2,
  MAR: 3, MARCH: 3,
  APR: 4, APRIL: 4,
  MAY: 5,
  JUN: 6, JUNE: 6,
  JUL: 7, JULY: 7,
  AUG: 8, AUGUST: 8,
  SEP: 9, SEPT: 9, SEPTEMBER: 9,
  OCT: 10, OCTOBER: 10,
  NOV: 11, NOVEMBER: 11,
  DEC: 12, DECEMBER: 12,
};
const IST_ZONE = "Asia/Kolkata";

const monthFromName = (folderName) => {
  const u = String(folderName || "").toUpperCase();
  for (const [k, v] of Object.entries(monthNameMap)) {
    if (u.includes(k)) return v;
  }
  return null;
};

const monthFolderPathFor = (folderPath) => {
  const parts = String(folderPath || "").split("/").filter(Boolean);
  let built = [];
  for (const part of parts) {
    built.push(part);
    if (/^\d+\.\s/.test(part)) return built.join("/");
  }
  return null;
};

const targetRoot = "Daily Wins";
const monthListPath = `${targetRoot}/Month List.md`;
const excludedBasenames = new Set(["months", "month list"]);

const files = app.vault.getFiles().map((f) => {
  const base = String(f.basename || "").toLowerCase();
  if (excludedBasenames.has(base)) return null;
  const monthFolderPath = monthFolderPathFor(f.parent?.path || "");
  return monthFolderPath ? { f, monthFolderPath } : null;
}).filter(Boolean);

const byFolder = new Map();
for (const item of files) {
  const folderPath = item.monthFolderPath;
  if (!byFolder.has(folderPath)) byFolder.set(folderPath, []);
  byFolder.get(folderPath).push(item.f);
}

const months = [...byFolder.entries()].map(([folderPath, list]) => {
  const leaf = folderLeaf(folderPath);
  const numPrefix = Number((leaf.match(/^\s*(\d{1,2})/) || [])[1] || 999);
  const detectedMonth = monthFromName(leaf);
  const first = list
    .map((f) => Number(f.stat?.ctime || Date.now()))
    .sort((a, b) => a - b)[0] || Date.now();
  const firstDt = dv.luxon.DateTime.fromMillis(first, { zone: IST_ZONE });
  const month = detectedMonth || firstDt.month;
  const year = firstDt.year;

  const workedByDay = new Map();
  for (const f of list) {
    const stamps = [f.stat?.ctime, f.stat?.mtime]
      .map((x) => Number(x || 0))
      .filter((x) => x > 0);
    for (const ts of stamps) {
      const d = dv.luxon.DateTime.fromMillis(ts, { zone: IST_ZONE });
      if (d.year !== year || d.month !== month) continue;
      if (!workedByDay.has(d.day)) workedByDay.set(d.day, []);
      workedByDay.get(d.day).push({ name: f.basename, path: f.path });
    }
  }

  for (const [day, items] of workedByDay.entries()) {
    const byPath = new Map();
    for (const item of items) {
      if (!item?.path) continue;
      if (!byPath.has(item.path)) byPath.set(item.path, item.name);
    }
    workedByDay.set(
      day,
      [...byPath.entries()]
        .map(([path, name]) => ({ path, name }))
        .sort((a, b) => a.name.localeCompare(b.name))
    );
  }

  const createdFiles = list
    .map((f) => {
      const createdAt = Number(f.stat?.ctime || 0);
      if (!createdAt) return null;
      const d = dv.luxon.DateTime.fromMillis(createdAt, { zone: IST_ZONE });
      if (d.year !== year || d.month !== month) return null;
      return {
        path: f.path,
        name: f.name || f.basename,
        createdAt,
      };
    })
    .filter(Boolean)
    .sort((a, b) => (a.createdAt - b.createdAt) || a.name.localeCompare(b.name));

  const dedupedCreatedFiles = [];
  const seen = new Set();
  for (const file of createdFiles) {
    if (seen.has(file.path)) continue;
    seen.add(file.path);
    dedupedCreatedFiles.push(file);
  }

  return {
    folderPath,
    leaf,
    numPrefix,
    month,
    year,
    workedByDay,
    createdFiles: dedupedCreatedFiles,
  };
}).sort((a, b) => (a.numPrefix - b.numPrefix) || a.leaf.localeCompare(b.leaf));

const generateMonthListBody = () => {
  const lines = ["# Month List", ""];
  for (const m of months) {
    lines.push(`## ${m.leaf}`);
    if (!m.createdFiles.length) {
      lines.push("- No files created in this month.");
      lines.push("");
      continue;
    }
    for (const file of m.createdFiles) {
      lines.push(`- [[${file.path}|${file.name}]]`);
    }
    lines.push("");
  }
  return lines.join("\n").trimEnd() + "\n";
};

const syncMonthList = async () => {
  const body = generateMonthListBody();
  const exists = await app.vault.adapter.exists(monthListPath);
  if (!exists) {
    await app.vault.adapter.write(monthListPath, body);
    return;
  }
  const current = await app.vault.adapter.read(monthListPath);
  if (current === body) return;
  await app.vault.adapter.write(monthListPath, body);
};

const root = dv.el("div", "", { cls: "months-cal-wrap" });
const syncRow = root.createEl("div", {
  attr: {
    style: "display:flex;justify-content:flex-end;margin-bottom:8px;",
  },
});
const syncBtn = syncRow.createEl("button", {
  cls: "months-btn",
  text: "Sync Month List",
});
syncBtn.style.position = "static";
syncBtn.style.transform = "none";
syncBtn.style.width = "auto";
syncBtn.style.height = "auto";
syncBtn.style.padding = "6px 10px";
syncBtn.style.fontSize = "12px";
syncBtn.addEventListener("click", async () => {
  try {
    await syncMonthList();
    syncBtn.textContent = "Synced";
    setTimeout(() => {
      syncBtn.textContent = "Sync Month List";
    }, 1200);
  } catch (err) {
    console.error("Month List sync failed:", err);
    syncBtn.textContent = "Sync Failed";
    setTimeout(() => {
      syncBtn.textContent = "Sync Month List";
    }, 1400);
  }
});

if (!months.length) {
  root.createEl("div", { text: "No month folders found. Use names like 3. FEB, 4. MARCH." });
} else {

let currentIndex = 0;

const header = root.createEl("div", { cls: "months-header" });
const titleEl = header.createEl("div", { cls: "months-title" });
const titleNav = header.createEl("div", { cls: "months-title-nav" });
const prevBtn = titleNav.createEl("button", { cls: "months-btn months-btn-left", text: "‹" });
const nextBtn = titleNav.createEl("button", { cls: "months-btn months-btn-right", text: "›" });

const tableWrap = root.createEl("div");
const stat = root.createEl("div", { cls: "months-tip" });

const moveTip = (ev) => {
  tooltip.style.left = `${ev.clientX + 12}px`;
  tooltip.style.top = `${ev.clientY + 12}px`;
};
let pinnedCell = null;
const hideTip = () => {
  tooltip.classList.remove("show");
  tooltip.style.pointerEvents = "none";
  tooltip.innerHTML = "";
};
const showHoverTip = (dateLabel, items, ev) => {
  tooltip.textContent = `${dateLabel}\n${items.map((i) => `- ${i.name}`).join("\n")}`;
  tooltip.style.pointerEvents = "none";
  moveTip(ev);
  tooltip.classList.add("show");
};
const showPinnedTip = (dateLabel, items, ev) => {
  tooltip.innerHTML = `<div class="months-tooltip-title">${escapeHtml(dateLabel)}</div>${items
    .map((i) => `<a href="#" class="months-tooltip-link" data-path="${escapeHtml(i.path)}">- ${escapeHtml(i.name)}</a>`)
    .join("")}`;
  tooltip.style.pointerEvents = "auto";
  moveTip(ev);
  tooltip.classList.add("show");
};

function renderMonth(idx) {
  pinnedCell = null;
  hideTip();
  currentIndex = Math.max(0, Math.min(idx, months.length - 1));

  const m = months[currentIndex];
  const first = dv.luxon.DateTime.fromObject({ year: m.year, month: m.month, day: 1 }, { zone: IST_ZONE });
  const monthName = first.toFormat("LLLL yyyy").toUpperCase();
  titleEl.textContent = monthName;

  const daysInMonth = first.daysInMonth;
  const startOffset = first.weekday % 7; // Sun=0
  const weekCols = Math.ceil((startOffset + daysInMonth) / 7);

  const matrix = Array.from({ length: 7 }, () => Array(weekCols).fill(null));
  for (let d = 1; d <= daysInMonth; d++) {
    const absolute = startOffset + (d - 1);
    const col = Math.floor(absolute / 7);
    const row = absolute % 7;
    matrix[row][col] = d;
  }

  const weekdayRows = ["SUN", "MON", "TUE", "WED", "THU", "FRI", "SAT"];
  let html = "<table class='months-grid'><thead><tr><th>WEEK</th>";
  for (let w = 1; w <= weekCols; w++) html += `<th>${w}</th>`;
  html += "</tr></thead><tbody>";

  for (let r = 0; r < 7; r++) {
    const rowCls = r === 0 ? "months-row-sun" : "";
    html += `<tr class='${rowCls}'><th class='months-row-label'>${weekdayRows[r]}</th>`;
    for (let c = 0; c < weekCols; c++) {
      const day = matrix[r][c];
      if (!day) {
        html += "<td class='months-empty'></td>";
        continue;
      }

      const worked = m.workedByDay.has(day);
      const items = worked ? m.workedByDay.get(day) : [];
      const tooltipText = worked
        ? `${first.set({ day }).toFormat("ccc, dd LLL yyyy")}\n${items.map((i) => `- ${i.name}`).join("\n")}`
        : "";

      const cls = `months-day-cell${worked ? " months-day-worked" : ""}`;
      const safeTip = tooltipText
        .replaceAll("&", "&amp;")
        .replaceAll("<", "&lt;")
        .replaceAll(">", "&gt;")
        .replaceAll('"', "&quot;");

      html += `<td class='${cls}' data-tip="${safeTip}"><span class='months-day'>${day}</span></td>`;
    }
    html += "</tr>";
  }

  html += "</tbody></table>";
  tableWrap.innerHTML = html;

  const workedDays = [...m.workedByDay.keys()].length;
  stat.textContent = `${workedDays} worked day${workedDays === 1 ? "" : "s"} in ${monthName}`;

  const cells = tableWrap.querySelectorAll(".months-day-cell.months-day-worked");
  for (const cell of cells) {
    const day = Number(cell.querySelector(".months-day")?.textContent || 0);
    const items = m.workedByDay.get(day) || [];
    const dateLabel = first.set({ day }).toFormat("ccc, dd LLL yyyy");
    cell.addEventListener("mouseenter", (ev) => {
      if (pinnedCell) return;
      if (!items.length) return;
      showHoverTip(dateLabel, items, ev);
    });
    cell.addEventListener("mousemove", (ev) => {
      if (pinnedCell) return;
      moveTip(ev);
    });
    cell.addEventListener("mouseleave", () => {
      if (pinnedCell) return;
      hideTip();
    });
    cell.addEventListener("dblclick", (ev) => {
      ev.preventDefault();
      ev.stopPropagation();
      if (pinnedCell === cell) {
        pinnedCell = null;
        hideTip();
        return;
      }
      pinnedCell = cell;
      showPinnedTip(dateLabel, items, ev);
    });
  }
}

prevBtn.addEventListener("click", () => renderMonth((currentIndex - 1 + months.length) % months.length));
nextBtn.addEventListener("click", () => renderMonth((currentIndex + 1) % months.length));

document.addEventListener("scroll", hideTip, true);
window.addEventListener("blur", hideTip);
tooltip.addEventListener("click", (ev) => {
  const a = ev.target.closest(".months-tooltip-link");
  if (!a) return;
  ev.preventDefault();
  const path = a.getAttribute("data-path");
  if (path) app.workspace.openLinkText(path, "", false);
});
document.addEventListener("click", (ev) => {
  if (!pinnedCell) return;
  const clickedCell = ev.target.closest(".months-day-cell");
  const inTip = ev.target.closest(`#${tooltipId}`);
  if (clickedCell || inTip) return;
  pinnedCell = null;
  hideTip();
});

renderMonth(0);
}
```
