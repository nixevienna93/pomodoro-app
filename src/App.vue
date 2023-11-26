<template>
  <h1>Pomodoro App by Popoy</h1>

  <div>
    <Timer title="Work Timer" :givenTime="(25 * 60)" cookieID="pomodoro-work" />
  </div>

  <hr>

  <div>
    <Timer title="Break Timer" :givenTime="(5 * 60)" cookieID="pomodoro-break" />
  </div>

  <hr>

  <div>
    <Timer title="Long Break Timer" :givenTime="(15 * 60)" cookieID="pomodoro-long-break" />
  </div>

  <p>Guide: Every 4 Pomodoros, take a longer 15-30 minute break</p>
  
</template>

<script>
import Timer from '@/components/Timer.vue'
export default {
  name: 'App',
  components: {
    Timer
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
