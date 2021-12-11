<template>
  <div
      class="calendar"
      ref="calendar"
      @mousedown="startSelect"
  >
    <div class="calendar-header">
      <div class="all-day-header">ALL DAY</div>
      <div v-for="(day, index) of Array(8)" :key="day" class="hours-header">{{ time(index) }}</div>
    </div>
    <div class="days">
      <div v-for="(day, index) of calendar" :key="index">{{ index }}</div>
    </div>
    <div class="days all-day">
      <div
          v-for="(day, index) of calendar"
          :key="index"
          @click="selectAllDay(index)"
          :class="{select: isEmptyDays[index]}"
      >
        <img src="https://icon-library.com/images/done-icon/done-icon-17.jpg" alt="">
      </div>
    </div>
    <div>
      <Day
          v-for="(day, index) of calendar"
          ref="days"
          :key="index"
          :day="day"
          :dayKeyName="index"
      />
    </div>
  </div>
</template>

<script>
import Day from "./Day";

export default {
  name: "Calendar",
  props: ["calendar"],
  components: {
    Day
  },
  data() {
    return {
      startSelected: null,
      endSelected: null,
      selected: null,
    }
  },
  computed: {
    isEmptyDays() {
      return Object.keys(this.calendar).reduce((acc, dayKey) => {
        acc[dayKey] = this.isDaySelected(this.calendar[dayKey]);

        return acc;
      }, {});
    },
  },
  methods: {
    time(i) {
      if (i * 3 > 9) {
        return `${i * 3}:00`
      }
      return `0${i * 3}:00`
    },
    isDaySelected(day) {
      return day.length
          && day.length === 1
          && (day[0].et - day[0].bt === 1439);

    },
    selectAllDay(key) {
      this.calendar[key] = this.isDaySelected(this.calendar[key])
          ? []
          : [
            {
              "bt": 0,
              "et": 1439
            }
          ]
    },
    startSelect(e) {
      this.$refs.calendar.addEventListener('mousemove', this.moveSelect);
      this.$refs.calendar.addEventListener('mouseup', this.stopSelect);
      this.$refs.calendar.addEventListener('mouseleave', this.stopSelect);

      this.$refs.days.forEach((day, dayIndex) => {
        day.$refs.hours.forEach((hour, hourIndex) => {
          if (e.target === hour.$el) {
            this.startSelected = {
              dayKeyName: day.dayKeyName,
              hourIndex: hourIndex,
              dayIndex: dayIndex,
              hour: hour
            };
            this.startSelected.hour.selectedTmp = true;
          }
        })
      })
    },
    moveSelect(e) {
      this.$refs.days.forEach((day, dayIndex) => {
        day.$refs.hours.forEach((hour, hourIndex) => {
          if (e.target === hour.$el && e.target !== this.startSelected.hour.$el) {
            this.endSelected ? this.endSelected.hour.selectedTmp = false : ''
            this.endSelected = {
              dayIndex: dayIndex,
              hourIndex: hourIndex,
              hour: hour
            };
            this.endSelected.hour.selectedTmp = true;
          }
        })
      })
    },
    stopSelect() {
      this.$refs.calendar.removeEventListener('mousemove', this.moveSelect);
      this.$refs.calendar.removeEventListener('mouseup', this.stopSelect);
      this.$refs.calendar.removeEventListener('mouseleave', this.stopSelect);

      if (!this.startSelected) {
        return
      }

      this.startSelected.hour.selectedTmp = false;

      if (this.endSelected) {
        this.endSelected.hour.selectedTmp = false;
      }

      if (!this.endSelected) {
        this.calendar[this.startSelected.dayKeyName].push({
          bt: this.startSelected.hourIndex * 60,
          et: this.startSelected.hourIndex * 60 + 59,
        })
        this.optimizationCalendar(this.startSelected.dayKeyName)
      } else {
        if (
            (this.startSelected.hourIndex > this.endSelected.hourIndex)
            || (this.startSelected.hourIndex > this.endSelected.hourIndex)
        ) {
          const tmp = this.startSelected
          this.startSelected = this.endSelected
          this.endSelected = tmp
        }
        Object.keys(this.calendar).forEach((elem, index) => {
          if ((index >= this.startSelected.dayIndex && index <= this.endSelected.dayIndex) ||
              (index >= this.endSelected.dayIndex && index <= this.startSelected.dayIndex)) {
            this.calendar[elem].push({
              bt: this.startSelected.hourIndex * 60,
              et: this.endSelected.hourIndex * 60 + 59,
            })
            this.optimizationCalendar(elem)
          }
        })
      }

      this.endSelected = null
      this.startSelected = null
    },
    optimizationCalendar(dayKeyName) {
      let day = this.calendar[dayKeyName]

      day.sort((a, b) => a.bt - b.bt)

      const optimizedDay = day.reduce((acc, current, i) => {
        const next = day[i + 1]
        const lastAcc = acc[acc.length - 1]

        if (lastAcc && lastAcc.et <= current.et && current.bt <= lastAcc.et + 1) {
          acc[acc.length - 1].et = current.et
        } else if (next && current.et >= (next.bt - 1)) {
          acc.push({
            bt: current.bt,
            et: Math.max(current.et, next.et)
          })
        } else if (!lastAcc || lastAcc.et < current.bt) {
          acc.push(current)
        }

        return acc
      }, [])
      this.$set(this.calendar, dayKeyName, optimizedDay)
    }

  }
}
</script>

<style>
.calendar {
  width: 317px;

  display: flex;
  flex-wrap: wrap;
}

.calendar-header {
  width: 100%;
  height: 30px;

  display: flex;
}

.calendar-header > .all-day-header {
  width: 25px;

  margin-left: 26px;
  padding: 2px 0;

  color: #616161;
  font-size: 10px;
}

.calendar-header > .hours-header {
  width: 33px;
  height: 20px;

  position: relative;
  display: flex;
  align-items: flex-end;

  font-size: 10px;
}

.calendar-header > .hours-header:before {
  content: "";
  height: 10px;

  position: absolute;
  top: 20px;
  left: 1px;

  border-left: 1px solid #616161;
}

.days {
  display: inline-block;
  margin-bottom: 10px;

  text-transform: uppercase;
  font-family: monospace, monospace;
  font-size: 15px;
}

.days > div {
  width: 25px;
  height: 25px;

  display: flex;
  align-items: center;
  justify-content: center;

  background: #F5F5F5;
  border: 1px solid #616161;
  border-bottom: none;
  border-right: none;
}

.days > div:last-child {
  border-bottom: 1px solid #616161;
}

.all-day img {
  width: 16px;
  height: 16px;

  display: none;

  background: white;
  border-radius: 50%;
}

.select img {
  display: block;
}

.all-day > div {
  background: #424242;
  border-color: #F5F5F5;
}

.all-day > div:first-child {
  border-color: #616161;
}
</style>