```dataviewjs 

dv.span("**🔗 Writing – Don’t break the chain! 🔗🔗🔗🔗**")

const calendarData = {
    entries: []
}

for (let page of dv.pages('"Crypto"')) {

    if (
        page.file.folder.includes("Daily Wins") &&
        page.writing === true
    ) {

        let date = page.file.mtime.toFormat("yyyy-MM-dd");

        calendarData.entries.push({
            date: date,
            content: await dv.span(`[🔗](${page.file.path})`)
        })
    }
}

renderHeatmapCalendar(this.container, calendarData)

```
