
```dataviewjs
(() => {
  const currentFolder = String(dv.current()?.file?.folder || "").trim();
  const targetRoot = currentFolder;
  const targetPrefix = targetRoot ? `${targetRoot}/` : "";
  const excludedBasenames = new Set(["months", "month list"]);
  const monthFolderPattern = /^\d+\.\s/;

  const monthFolderPathFor = (folderPath) => {
    const raw = String(folderPath || "");
    if (targetRoot && !raw.startsWith(targetPrefix)) return null;
    const rel = targetRoot ? raw.slice(targetPrefix.length) : raw;
    const parts = rel.split("/").filter(Boolean);
    let built = [];
    for (const part of parts) {
      built.push(part);
      if (/^\d+\.\s/.test(part)) {
        const relPath = built.join("/");
        return targetRoot ? `${targetRoot}/${relPath}` : relPath;
      }
    }
    return null;
  };

  const allEntries = app.vault.getAllLoadedFiles();
  const monthFolders = allEntries
    .filter((entry) => {
      const path = String(entry?.path || "");
      if (targetRoot) {
        if (!path.startsWith(targetPrefix)) return false;
      }
      const rel = targetRoot ? path.slice(targetPrefix.length) : path;
      const parts = rel.split("/").filter(Boolean);
      return parts.length === 1 && monthFolderPattern.test(parts[0]);
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
      if (targetRoot) {
        if (!String(f.path || "").startsWith(targetPrefix)) return false;
      }
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

<style>
  .graph-links { display: none; }
  .markdown-source-view .graph-links { display: none; }
  .markdown-source-view.mod-cm6 .graph-links { display: none; }
  .markdown-reading-view .graph-links { display: none; }
</style>
<!-- GRAPH-LINKS:START -->
<div class="graph-links">
- [[2. FEB/Miscellaneous/Blockchain and Crypto Facts.md]]
- [[2. FEB/Miscellaneous/Miners and Mining Software.md]]
- [[2. FEB/Miscellaneous/Nonce Mining and Coin Creation.md]]
- [[2. FEB/Miscellaneous/Rust Setup.md]]
- [[2. FEB/Rust/Hello_World.md]]
- [[2. FEB/Videos/7 Cryptography Concepts EVERY Developer Should Know.md]]
- [[2. FEB/Videos/But how does bitcoin actually work.md]]
- [[2. FEB/Videos/Getting Started with Rust  Rust Book.md]]
- [[2. FEB/Videos/Videos.md]]
</div>
<!-- GRAPH-LINKS:END -->
