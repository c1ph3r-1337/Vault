```dataviewjs 


dv.span("**🔗 Writing **- Dont break the chain! 🔗🔗🔗🔗")

const calendarData = {
    year: 2022, // optional, remove this line to autoswitch year
    colors: {
        //white: ["#fff","#fff","#fff","#fff","#fff"],
        transparent: ["transparent"],
    },
    entries: []
}

for(let page of dv.pages('"daily notes"').where(p=>p.writing)){
	 
    calendarData.entries.push({
        date: page.file.name,
        content: await dv.span(`[🔗](${page.file.name})`), //for hover preview
    }) 
}

//console.log(calendarData)
	
renderHeatmapCalendar(this.container, calendarData)


```