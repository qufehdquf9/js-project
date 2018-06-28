<template>
  <div class="Calendar">
    <div class="section">
        <div class="container" id="calendar_header">
        </div>
    </div>
    <div class="section">
        <div class="container" @click="push_mark" id="calendar_calendar">
           <vue-event-calendar :events="demoEvents"  @day-changed="handleDayChanged" @month-changed="handleMonthChanged"></vue-event-calendar>
            <template scope="props">
              <div v-for="(event, index) in props.showEvents" class="event-item" v-bind:key="event, index">
                {{event}}
              </div>
          </template>
        </div>
    </div>
  </div>
</template>

<script>
let today = new Date()
let i = 0
let tempfirst = ['2018-06-01', '2018-06-29']
export default {
  created () {
    this.getDate(tempfirst)
    this.$http.get('http://13.125.254.107:3030/calendar')
    .then((response) => {
      this.firstDay = response.data.beginDate
      this.endDay = response.data.endDate
    })
  },
  name: 'calendar',
  data () {
    return {
      demoEvents: [{
        date: `${today.getFullYear()}/${today.getMonth() + 1}/${today.getDate()}`,
        title: 'today',
        desc: 'today'
      }],
      firstDay: '',
      endDay: ''
    }
  },
  methods: {
    sendPost: function (event) {
      this.$http.post('http://13.125.254.107:3030/calendars', {
        beginDate: this.firstDay,
        endDate: this.endDay
      })
      .then((response) => {
        if (response.status === 200) {
          this.$router.push('/calendar')
        }
      }, function () {
        console.log('failed')
      })
    },
    getDate (tempfirst) {
      for (let i = 0; i < tempfirst.length; i++) {
        let dateDB = tempfirst[i].split('-')
        this.add_mark(dateDB)
      }
    },
    handleDayChanged (data) {
    },
    handleMonthChanged (data) {
    },
    cal_check (year, month, day, data) {
      if ((month === 4 || month === 6 || month === 9 || month === 11) && day > 30) {
        month = month + 1
        day = day - 30
        data = {
          date: `${year}/${month}/${day}`,
          title: `${month}/${day}`,
          desc: '생리 예정' + `${i + 1}` + '일차'
        }
      } else if (month === 2 && day > 28) {
        month = month + 1
        day = day - 28
        data = {
          date: `${year}/${month}/${day}`,
          title: `${month}/${day}`,
          desc: '생리 예정' + `${i + 1}` + '일차'
        }
      } else if ((month === 1 || month === 3 || month === 5 || month === 7 || month === 8 || month === 10) && day > 31) {
        month = month + 1
        day = day - 31
        data = {
          date: `${year}/${month}/${day}`,
          title: `${month}/${day}`,
          desc: '생리 예정' + `${i + 1}` + '일차'
        }
      } else if (month === 12 && day > 31) {
        month = 1
        day = day - 31
        year = year + 1
        data = {
          date: `${year}/${month}/${day}`,
          title: `${month}/${day}`,
          desc: '생리 예정' + `${i + 1}` + '일차'
        }
      }
      return data
    },
    add_mark (dateDB) {
      let year = parseFloat(dateDB[0])
      let month = parseFloat(dateDB[1])
      let day = parseFloat(dateDB[2])
      let data = {
        date: `${year}/${month}/${day}`,
        title: `${month}/${day}`,
        desc: '생리 예정' + 1 + '일차'
      }
      for (let j = 0; j < 28; j++) {
        if (j < 5) {
          data = {
            date: `${year}/${month}/${day}`,
            title: `${month}/${day}`,
            desc: '생리 예정' + j + '일차'
          }
          data = this.cal_check(year, month, day, data)
          this.demoEvents.push(data)
        } else if (j > 5 && j < 16) {
          data = {
            date: `${year}/${month}/${day}`,
            title: `${month}/${day}`,
            desc: '임신 가능성이 높습니다.'
          }
          data = this.cal_check(year, month, day, data)
          this.demoEvents.push(data)
        } else {
          data = {
            date: `${year}/${month}/${day}`,
            title: `${month}/${day}`,
            desc: '임신 가능성이 적습니다.'
          }
        }
        day = day + 1
      }
    },
    push_mark (e) {
      let yearMonth = document.getElementsByClassName('title').item(0).innerHTML
      let divideYm = yearMonth.split('/')
      if (e.target.className === 'date-num' && e.target.innerHTML > 0) {
        let day1 = parseFloat(e.target.innerHTML)
        let day2 = parseFloat(e.target.innerHTML) + 4
        let month = parseFloat(divideYm[0])
        let year = parseFloat(divideYm[1])
        let data = {
          date: `${year}/${month}/${day2}`,
          title: `${month}/${day2}`,
          desc: '생리 예정'
        }
        this.cal_check(year, month, day2, data)
        this.demoEvents.push(data)
        this.firstDay = `${year}/${month}/${day1}`
        // alert(this.firstDay)
        this.endDay = `${year}/${month}/${day2}`
        // alert(this.endDay)
        this.sendPost()
      }
    }
  }
}
</script>
