<template>
  <div>
    <q-toolbar>
      <q-btn stretch flat label="Prev" @click="calendarPrev" />
      <q-separator vertical />
      <q-btn stretch flat label="Next" @click="calendarNext" />
      <q-space />
    </q-toolbar>
    <q-separator />
    <div style="overflow: hidden;">
      <q-calendar
        ref="calendar"
        v-model="selectedDate"
        view="month"
        locale="en-us"
        mini-mode="auto"
        breakpoint="sm"
        animated
        transition-prev="slide-right"
        transition-next="slide-left"
        :selected-start-end-dates="startEndDates"
        :day-class="classDay"
        @mousedown:day="onMouseDownDay"
        @mouseup:day="onMouseUpDay"
        @mousemove:day="onMouseMoveDay"
      />
    </div>
  </div>
</template>

<script>
import {
  getDayIdentifier
} from 'ui' // ui is aliased from '@quasar/quasar-ui-qcalendar'

export default {
  data () {
    return {
      selectedDate: '',
      anchorTimestamp: '',
      otherTimestamp: '',
      mouseDown: false
    }
  },

  computed: {
    startEndDates () {
      const dates = []
      if (this.anchorDayIdentifier !== false && this.otherDayIdentifier !== false) {
        if (this.anchorDayIdentifier <= this.otherDayIdentifier) {
          dates.push(this.anchorTimestamp.date, this.otherTimestamp.date)
        } else {
          dates.push(this.otherTimestamp.date, this.anchorTimestamp.date)
        }
      }
      return dates
    },
    anchorDayIdentifier () {
      if (this.anchorTimestamp !== '') {
        return getDayIdentifier(this.anchorTimestamp)
      }
      return false
    },
    otherDayIdentifier () {
      if (this.otherTimestamp !== '') {
        return getDayIdentifier(this.otherTimestamp)
      }
      return false
    },
    lowIdentifier () {
      // returns lowest of the two values
      return Math.min(this.anchorDayIdentifier, this.otherDayIdentifier)
    },
    highIdentifier () {
      // returns highest of the two values
      return Math.max(this.anchorDayIdentifier, this.otherDayIdentifier)
    }
  },

  methods: {
    calendarNext () {
      this.$refs.calendar.next()
    },

    calendarPrev () {
      this.$refs.calendar.prev()
    },

    classDay (timestamp) {
      if (this.anchorDayIdentifier !== false && this.otherDayIdentifier !== false) {
        return this.getBetween(timestamp)
      }
    },

    getBetween (timestamp) {
      const nowIdentifier = getDayIdentifier(timestamp)
      return {
        'q-selected-day-first': this.lowIdentifier === nowIdentifier,
        'q-selected-day': this.lowIdentifier <= nowIdentifier && this.highIdentifier >= nowIdentifier,
        'q-selected-day-last': this.highIdentifier === nowIdentifier
      }
    },

    onMouseDownDay (e) {
      // mouse is down, start selection and capture current
      this.mouseDown = true
      this.anchorTimestamp = e
      this.otherTimestamp = e
    },

    onMouseUpDay (e) {
      // mouse is up, capture last and cancel selection
      this.otherTimestamp = e
      this.mouseDown = false
    },

    onMouseMoveDay (e) {
      if (this.mouseDown === true) {
        this.otherTimestamp = e
      }
    }
  }
}
</script>
