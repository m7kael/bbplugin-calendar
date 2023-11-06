<script>
  
  import { getContext } from "svelte"	
  import FullCalendar, { Calendar } from 'svelte-fullcalendar'
  import daygridPlugin from '@fullcalendar/daygrid'
  import timeGridPlugin from '@fullcalendar/timegrid'
  import listPlugin from '@fullcalendar/list'
  import tippy from 'tippy.js';
  //import 'fullcalendar/main.css'; // Import FullCalendar's CSS
  import 'tippy.js/dist/tippy.css'; // Import Tippy.js CSS

  // Data provider 1
  export let dataProvider
  export let mappingTitle
  export let mappingDate
  export let mappingStart
  export let mappingEnd
  export let mappingColor
  export let defaultColor
  export let allday

  // Data provider 2
  export let dataProvider2
  export let mappingTitle2
  export let mappingDate2
  export let mappingStart2
  export let mappingEnd2
  export let mappingColor2
  export let defaultColor2
  export let allday2

  // Settings
  export let headerOptionsStart
  export let headerOptionsCenter
  export let headerOptionsEnd
  export let showWeek
  export let firstDay
  export let firstView
  export let dateStart
  export let language

  // Events
  export let calendarEvent
  export let calendarDate

  // Defining variables
  let eventsList = [];
  let calendarRef

  // Check if date is defined 
  if(!dateStart) {
    dateStart = Date.now()
  }

  // Map events
  if(dataProvider.rows) {
      dataProvider.rows.forEach( row => {
        let eventColor = mappingColor ?? defaultColor  
        eventsList.push({
          title: row[mappingTitle],
          date: row[mappingDate],
          color: eventColor,
          start: row[mappingStart],
          end: row[mappingEnd],
          allDay: allday,
          event: row
        })
      })
    }
    if(dataProvider2.rows) {
      dataProvider.rows.forEach( row => {
        let eventColor = mappingColor2 ?? defaultColor2  
        eventsList.push({
          title: row[mappingTitle2],
          date: row[mappingDate2],
          color: eventColor,
          start: row[mappingStart2],
          end: row[mappingEnd2],
          allDay: allday2,
          event: row
        })
      })
    }

  let options = {
    events: eventsList,
    headerToolbar: {
      start: headerOptionsStart,
      center: headerOptionsCenter,
      end: headerOptionsEnd
    },
    plugins: [daygridPlugin, listPlugin, timeGridPlugin],
    initialView: firstView,
    locale: language,
    initialDate:  new Date(dateStart),
    firstDay: firstDay,
    weekNumbers: showWeek,
    dayMaxEvents: true,

    customButtons: {
      prevButton: {
        text: 'Prev',
        icon: 'chevron-left',
        click: ()=> {
          const calendarApi = calendarRef.getAPI()
          calendarApi.prev()
          calendarDate({
            value: calendarApi.getDate(),
            view: calendarApi.view.type
          })
        }
      },
      nextButton: {
        text: 'Next',
        icon: 'chevron-right',
        click: ()=> {
          const calendarApi = calendarRef.getAPI()
          calendarApi.next()
          
          calendarDate({
            value: calendarApi.getDate(),
            view: calendarApi.view.type
          })
        }
      },
      todayButton: {
        text: 'Today',  
        click: ()=> {
          const calendarApi = calendarRef.getAPI()
          calendarApi.today()
          calendarDate({
            value: calendarApi.getDate(),
            view: calendarApi.view.type
          })
        }
      }
    },

    eventDidMount: function (info) {
      const tooltip = tippy(info.el, {
        content: info.event.title,
        placement: 'top',
        trigger: 'mouseenter',
        container: 'body'
      });
    },

    eventClick: (event)=>{
      calendarEvent({
        value: event.event
      })
    }
  }
  const { styleable } = getContext("sdk") 
  const component = getContext("component")

</script>

<main>

  <div use:styleable={$component.styles}>
    <FullCalendar bind:this={calendarRef} {options} />
  </div>

</main>
