<template>
  <div id="app">
    <Calendar :calendar="data"/>
  </div>
</template>

<script>
import Calendar from './components/Calendar.vue'

export default {
  name: 'App',
  components: {
    Calendar
  },
  data () {
    return {
      data: {
        "mo": [
          {
            "bt": 240,
            "et": 779
          }
        ],
        "tu": [
        ],
        "we": [
        ],
        "th": [
          {
            "bt": 240,
            "et": 779
          },
          {
            "bt": 1140,
            "et": 1319
          }
        ],
        "fr": [
          {
            "bt": 660,
            "et": 1019
          }
        ],
        "sa": [
          {
            "bt": 0,
            "et": 1439
          }
        ],
        "su": []
      }
    }
  }
}
</script>

<style>
#app {
}
</style>
