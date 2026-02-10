```dataviewjs 
dv.span("**🔗 Writing – Don’t break the chain! 🔗🔗🔗🔗**")

const calendarData = {
    year: new Date().getFullYear(),
    entries: []
}

for (let page of dv.pages('"Crypto"')) {

    if (
        page.file.folder === "Crypto/Daily Wins" &&
        page.writing === true
    ) {

        calendarData.entries.push({
            date: page.file.mtime.toFormat("yyyy-MM-dd"),
            content: await dv.span(`[🔗](${page.file.path})`)
        })
    }
}

renderHeatmapCalendar(this.container, calendarData)

```
