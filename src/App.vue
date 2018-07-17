<template>
  <div id="app">
    <availabilityCalendar/>
  </div>
</template>

<script>
import availabilityCalendar from "./components/availability-calendar.vue";

export default {
  name: "App",
  components: {
    availabilityCalendar
  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
