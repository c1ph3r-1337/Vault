```dataviewjs 

dv.span("**🔗 Writing – Don’t break the chain! 🔗🔗🔗🔗**")

const calendarData = {
    // Auto-detects year
    colors: {
        transparent: ["transparent"],
    },
    entries: []
}

for (let page of dv.pages('"Crypto/Daily Wins"').where(p => p.writing === true)) {
    calendarData.entries.push({
        date: page.file.name,              // YYYY-MM-DD.md
        content: await dv.span(
            `[🔗](${page.file.path})`
        )
    })
}

renderHeatmapCalendar(this.container, calendarData)



```