<template>
   <div class="row">
      <div class="col-md-3">
         <div class="box box-success">
            <div class="box-header with-border">
               <h3 class="box-title">Add Event</h3>
               <div class="box-tools pull-right">
                  <button type="button" class="btn btn-box-tool" data-widget="remove"></button>
               </div>
            </div>
            <form>
            <div class="box-body">
               <div class="form-group">
                  <label>Type</label>
                  <select  v-model="form.type" class="form-control">
                     <option>Consultation</option>
                     <option>Examination</option>
                  </select>
               </div>
               <div class="form-group">
                  <label>Date:</label>
                  <div class="input-group date">
                     <div class="input-group-addon">
                        <i class="fa fa-calendar"></i>
                     </div>
                     <input v-model="form.date" type="date" class="form-control pull-right">
                  </div>
               </div>
                <button type="button" class="btn btn-block btn-success" @click ="createSchedule()">Request Schedule</button>
            </div>
            </form>
         </div>
      </div>
      <div class="col-md-9">
         <div class="box box-success">
            <div class="box-header with-border">
            <h3 class="box-title">Calendar Goes Here</h3>
            </div>
            <FullCalendar
      class='demo-app-calendar'
      ref="fullCalendar"
      defaultView="dayGridMonth"
      :header="{
        left: 'prev,next today',
        center: 'title',
        right: 'dayGridMonth,timeGridWeek,timeGridDay,listWeek'
      }"
      :plugins="calendarPlugins"
      :weekends="calendarWeekends"
      :events="calendarEvents"
      @dateClick="handleDateClick"
      @eventClick="handleEventClick"
      />
         </div>
      </div>
   </div>
</template>
<script>
   import FullCalendar from '@fullcalendar/vue'
   import dayGridPlugin from '@fullcalendar/daygrid'
   import interactionPlugin from '@fullcalendar/interaction'
   import timeGridPlugin from '@fullcalendar/timegrid'
   export default {
      components: {
         FullCalendar
      },
      data(){
         return{
            calendarPlugins: [
               dayGridPlugin,
               timeGridPlugin,
               interactionPlugin
            ],
            calendarWeekends: true,
            calendarEvents: [ // initial event data
               
            ],
            form: new Form({
               date:'',
               type:''
            })
         }
      },
      methods:{
          createSchedule(){
            this.form.post('/addSchedule')
              .then(({data})=>{
                swal.fire("Schedule Created!", "", "success");
                let classColor
                if(data.schedule_type == 'Examination'){
                     classColor = 'examination'
                } else {
                     classColor = 'consultation'
                }
                this.calendarEvents.push({ // add new event
                  id: data.id,
                  title: data.schedule_type,
                  start: data.sched_date,
                  className: classColor
               })
              })
          },
          getSchedules(){
             axios.post('/getSchedule')
               .then(({data})=>{
                  let classColor
                  data.forEach(res=>{
                     if(res.schedule_type == 'Examination'){ //set the class name of the schedule type. see css
                        classColor = 'examination'
                     } else {
                        classColor = 'consultation'
                     }
                     this.calendarEvents.push({ // set all the event 
                        id: res.id,
                        title: res.schedule_type,
                        start: res.sched_date,
                        className: classColor
                     })
                  })
               })
          },
          handleDateClick(arg) { // click date
            alert('date clicked') 
         },
          handleEventClick(arg) { // click event
            console.log(arg.event.id) //get the id of the clicked event
         }
      },
       mounted() {
          this.getSchedules()
       }
   }
</script>
<style lang='scss'>
// you must include each plugins' css
// paths prefixed with ~ signify node_modules
@import '~@fullcalendar/core/main.css';
@import '~@fullcalendar/daygrid/main.css';
@import '~@fullcalendar/timegrid/main.css';
.demo-app {
  font-family: Arial, Helvetica Neue, Helvetica, sans-serif;
  font-size: 14px;
}
.demo-app-top {
  margin: 0 0 3em;
}
.demo-app-calendar {
  margin: 0 auto;
  max-width: 900px;
}
.examination {
   background: red;
   color: #ffffff;
   border-color: red;
}
.consultation {
   background: green;
   color: #ffffff;
   border-color: green;
}
</style>