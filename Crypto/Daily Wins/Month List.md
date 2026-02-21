# Month List

```dataviewjs
(() => {
  const targetRoot = "Daily Wins";
  const excludedBasenames = new Set(["months", "month list"]);
  const monthFolderPattern = /^\d+\.\s/;

  const monthFolderPathFor = (folderPath) => {
    const parts = String(folderPath || "").split("/").filter(Boolean);
    let built = [];
    for (const part of parts) {
      built.push(part);
      if (/^\d+\.\s/.test(part)) return built.join("/");
    }
    return null;
  };

  const allEntries = app.vault.getAllLoadedFiles();
  const monthFolders = allEntries
    .filter((entry) => {
      const path = String(entry?.path || "");
      if (!path.startsWith(`${targetRoot}/`)) return false;
      const parts = path.split("/").filter(Boolean);
      return parts.length === 2 && monthFolderPattern.test(parts[1]);
    })
    .map((entry) => {
      const path = String(entry.path || "");
      const leaf = path.split("/").filter(Boolean).pop() || path;
      const order = Number((leaf.match(/^\s*(\d{1,2})/) || [])[1] || 999);
      return { path, leaf, order };
    })
    .sort((a, b) => (a.order - b.order) || a.leaf.localeCompare(b.leaf));

  const files = app.vault.getFiles()
    .filter((f) => {
      const base = String(f.basename || "").toLowerCase();
      if (excludedBasenames.has(base)) return false;
      if (!String(f.path || "").startsWith(`${targetRoot}/`)) return false;
      if (String(f.path || "").split("/").some((segment) => segment.startsWith("."))) return false;
      return true;
    })
    .map((f) => {
      const monthFolderPath = monthFolderPathFor(f.parent?.path || "");
      return monthFolderPath ? { file: f, monthFolderPath } : null;
    })
    .filter(Boolean);

  const byMonth = new Map(monthFolders.map((m) => [m.path, []]));
  for (const item of files) {
    if (!byMonth.has(item.monthFolderPath)) byMonth.set(item.monthFolderPath, []);
    byMonth.get(item.monthFolderPath).push(item.file);
  }

  const months = monthFolders
    .map((m) => {
      const list = byMonth.get(m.path) || [];
      const sorted = list
        .map((f) => ({
          path: f.path,
          name: f.basename,
          ts: Number(f.stat?.ctime || f.stat?.mtime || 0),
        }))
        .sort((a, b) => (a.ts - b.ts) || a.name.localeCompare(b.name));
      return { path: m.path, leaf: m.leaf, order: m.order, files: sorted };
    });

  if (!months.length) {
    dv.paragraph("No month folders found.");
    return;
  }

  for (const month of months) {
    dv.header(2, month.leaf);
    if (!month.files.length) {
      dv.list(["No files found."]);
      continue;
    }
    dv.list(month.files.map((f) => dv.fileLink(f.path, false, f.name)));
  }
})();
```
