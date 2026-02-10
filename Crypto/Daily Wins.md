```dataviewjs 

dv.span("**🔗 Writing – Don’t break the chain! 🔗🔗🔗🔗**")

const calendarData = {
    entries: []
}

for (let page of dv.pages('"Crypto/Daily Wins"')
        .where(p => p.writing === true && p.created)) {

    calendarData.entries.push({
        date: dv.date(page.created).toFormat("yyyy-MM-dd"),
        content: await dv.span(`[🔗](${page.file.path})`)
    })
}

renderHeatmapCalendar(this.container, calendarData)


```