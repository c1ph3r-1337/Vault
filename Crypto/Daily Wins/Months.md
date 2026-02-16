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
    .months-cal-toolbar {
      display: grid;
      grid-template-columns: auto minmax(0, 1fr) auto;
      gap: 8px;
      align-items: center;
      margin-bottom: 10px;
    }
    .months-nav {
      display: inline-flex;
      gap: 6px;
    }
    .months-btn,
    .months-select {
      border: 1px solid #455364;
      background: linear-gradient(180deg, #2a3544, #202a36);
      color: #dbe3ed;
      border-radius: 10px;
      font-size: 12px;
      font-weight: 700;
      padding: 7px 10px;
      height: 34px;
      box-sizing: border-box;
    }
    .months-btn {
      cursor: pointer;
      min-width: 36px;
      display: inline-flex;
      align-items: center;
      justify-content: center;
    }
    .months-btn:hover {
      filter: brightness(1.08);
      border-color: color-mix(in srgb, var(--interactive-accent) 45%, #455364);
    }
    .months-btn:focus-visible,
    .months-select:focus-visible {
      outline: none;
      border-color: color-mix(in srgb, var(--interactive-accent) 55%, #455364);
      box-shadow: 0 0 0 2px color-mix(in srgb, var(--interactive-accent) 24%, transparent);
    }
    .months-title {
      text-align: center;
      margin: -2px auto 8px;
      font-size: 2rem;
      line-height: 1.1;
      font-weight: 900;
      letter-spacing: 0.04em;
      color: color-mix(in srgb, var(--interactive-accent) 88%, #2ad46d);
      display: flex;
      align-items: center;
      justify-content: center;
      transform: translateY(-4px);
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
      margin-top: 10px;
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
    .months-tooltip.show {
      opacity: 1;
      transform: translateY(0);
    }
    @media (max-width: 860px) {
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

const monthFromName = (folderName) => {
  const u = String(folderName || "").toUpperCase();
  for (const [k, v] of Object.entries(monthNameMap)) {
    if (u.includes(k)) return v;
  }
  return null;
};

const files = app.vault.getMarkdownFiles().filter((f) => {
  const leaf = folderLeaf(f.parent?.path || "");
  return /^\d+\.\s/.test(leaf);
});

const byFolder = new Map();
for (const f of files) {
  const folderPath = f.parent?.path || "";
  if (!byFolder.has(folderPath)) byFolder.set(folderPath, []);
  byFolder.get(folderPath).push(f);
}

const months = [...byFolder.entries()].map(([folderPath, list]) => {
  const leaf = folderLeaf(folderPath);
  const numPrefix = Number((leaf.match(/^\s*(\d{1,2})/) || [])[1] || 999);
  const detectedMonth = monthFromName(leaf);
  const first = list
    .map((f) => Number(f.stat?.ctime || Date.now()))
    .sort((a, b) => a - b)[0] || Date.now();
  const firstDt = dv.luxon.DateTime.fromMillis(first);
  const month = detectedMonth || firstDt.month;
  const year = firstDt.year;

  const workedByDay = new Map();
  for (const f of list) {
    const stamps = [f.stat?.ctime, f.stat?.mtime]
      .map((x) => Number(x || 0))
      .filter((x) => x > 0);
    for (const ts of stamps) {
      const d = dv.luxon.DateTime.fromMillis(ts);
      if (d.year !== year || d.month !== month) continue;
      if (!workedByDay.has(d.day)) workedByDay.set(d.day, []);
      workedByDay.get(d.day).push(f.basename);
    }
  }

  for (const [day, names] of workedByDay.entries()) {
    workedByDay.set(day, [...new Set(names)].sort());
  }

  return {
    folderPath,
    leaf,
    numPrefix,
    month,
    year,
    workedByDay,
  };
}).sort((a, b) => (a.numPrefix - b.numPrefix) || a.leaf.localeCompare(b.leaf));

const root = dv.el("div", "", { cls: "months-cal-wrap" });
if (!months.length) {
  root.createEl("div", { text: "No month folders found. Use names like 3. FEB, 4. MARCH." });
} else {

let currentIndex = 0;

const toolbar = root.createEl("div", { cls: "months-cal-toolbar" });
const nav = toolbar.createEl("div", { cls: "months-nav" });
const prevBtn = nav.createEl("button", { cls: "months-btn", text: "<" });
const nextBtn = nav.createEl("button", { cls: "months-btn", text: ">" });
const picker = toolbar.createEl("select", { cls: "months-select" });
const stat = toolbar.createEl("div", { cls: "months-tip" });

for (let i = 0; i < months.length; i++) {
  const m = months[i];
  const title = dv.luxon.DateTime.fromObject({ year: m.year, month: m.month, day: 1 }).toFormat("LLLL yyyy");
  const opt = picker.createEl("option", { text: `${m.leaf} • ${title}` });
  opt.value = String(i);
}

const titleEl = root.createEl("div", { cls: "months-title" });
const tableWrap = root.createEl("div");

const moveTip = (ev) => {
  tooltip.style.left = `${ev.clientX + 12}px`;
  tooltip.style.top = `${ev.clientY + 12}px`;
};
const hideTip = () => tooltip.classList.remove("show");

function renderMonth(idx) {
  currentIndex = Math.max(0, Math.min(idx, months.length - 1));
  picker.value = String(currentIndex);

  const m = months[currentIndex];
  const first = dv.luxon.DateTime.fromObject({ year: m.year, month: m.month, day: 1 });
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
      const names = worked ? m.workedByDay.get(day) : [];
      const tooltipText = worked
        ? `${first.set({ day }).toFormat("ccc, dd LLL yyyy")}\n${names.map((n) => `- ${n}`).join("\n")}`
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
    cell.addEventListener("mouseenter", (ev) => {
      const txt = cell.getAttribute("data-tip") || "";
      if (!txt) return;
      tooltip.innerHTML = txt;
      moveTip(ev);
      tooltip.classList.add("show");
    });
    cell.addEventListener("mousemove", moveTip);
    cell.addEventListener("mouseleave", hideTip);
  }
}

picker.addEventListener("change", () => renderMonth(Number(picker.value || 0)));
prevBtn.addEventListener("click", () => renderMonth((currentIndex - 1 + months.length) % months.length));
nextBtn.addEventListener("click", () => renderMonth((currentIndex + 1) % months.length));

document.addEventListener("scroll", hideTip, true);
window.addEventListener("blur", hideTip);

renderMonth(0);
}
```
