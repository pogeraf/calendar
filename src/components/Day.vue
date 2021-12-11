<template>
  <div class="day">
    <Hours
        v-for="(day, index) of Array(24)"
        ref="hours"
        :key="`${dayKeyName}${index}`"
        :selected="daySelected(index)"
    />
  </div>
</template>

<script>
import Hours from "./Hours";

export default {
  name: "Day",
  components: {
    Hours
  },
  props: ["day", "dayKeyName"],
  methods: {
    daySelected (hour) {
      for (let item of this.day) {
        if ((item.bt / 60 <= hour) && ((item.et + 1) / 60 > hour)) {
          return true
        }
      }
      return false
    },
  },
}
</script>

<style >
.day {
  height: 26px;
}
.day:last-child > .hour{
  border-bottom: 1px solid #616161;
}
.day > .hour:first-child {
  border-left: 1px solid #616161;
}
</style>