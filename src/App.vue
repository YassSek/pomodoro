<template>
  <h1>Pomodoro</h1>
  <div id="app">
    <Timer />
  </div>
</template>

<script>
import Timer from "@/components/Timer.vue";

export default {
  name: "App",
  components: {
    Timer
  }
};
</script>

<style>
@import url("https://fonts.googleapis.com/css2?family=Roboto&display=swap");

body {
  background-color: hsl(235, 21%, 11%);
  color: rgb(204, 204, 204);
  font-family: "Roboto", sans-serif;
}
h1 {
  margin-left: 41%;
}
</style>
