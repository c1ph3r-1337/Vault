```dataviewjs 
dv.span("**🔗 Writing – Don’t break the chain! 🔗🔗🔗🔗**")

const calendarData = {
    year: new Date().getFullYear(),   // ⭐ REQUIRED FIX
    entries: []
}

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
