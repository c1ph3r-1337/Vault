```dataviewjs 

dv.span("**🔗 Writing – Don’t break the chain! 🔗🔗🔗🔗**")

const calendarData = {
    entries: []
}

for (let page of dv.pages('"Crypto/Daily Wins"')) {

    if (page.writing === true && page.created) {
        calendarData.entries.push({
            date: page.created.split("T")[0],   // ← KEY FIX
            content: await dv.span(`[🔗](${page.file.path})`)
        })
    }
}

renderHeatmapCalendar(this.container, calendarData)


```