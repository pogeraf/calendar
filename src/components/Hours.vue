<template>
  <div class="hour" :class="{selected, selectedTmp}"></div>
</template>

<script>
export default {
  name: "Hours",
  props: ["selected"],
  data() {
    return {
      selectedTmp: false,
    }
  }
}
</script>

<style>
.hour {
  width: 10px;
  height: 25px;

  display: inline-block;

  background: #F5F5F5;
  border: 1px solid #616161;
  border-left: none;
  border-bottom: none;
}
.hour.selected {
  background: #BDBDBD;
}
.hour.selectedTmp {
  background: #F4FF81;
}
</style>