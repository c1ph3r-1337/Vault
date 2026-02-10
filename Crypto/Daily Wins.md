```dataviewjs 

dv.span("**🔗 Writing – Don’t break the chain! 🔗🔗🔗🔗**")

const calendarData = { entries: [] }

for (let page of dv.pages('"Crypto"')) {

    if (
        page.file.folder.startsWith("Crypto/Daily Wins") &&
        page.writing === true &&
        page.created
    ) {
        calendarData.entries.push({
            date: page.created.toString().split("T")[0],
            content: await dv.span(`[🔗](${page.file.path})`)
        })
    }
}

renderHeatmapCalendar(this.container, calendarData)
```

```dataviewjs
for (let p of dv.pages('"Crypto"')) {
  if (p.writing === true) {
    dv.paragraph(
      `FOUND → ${p.file.path} | created=${p.created}`
    )
  }
}

```