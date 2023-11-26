<template>
    <div>
        <h2>{{ title}}</h2>
        <div class="timer-wrap">
            {{ formatTime }}
        </div>
        <div class="timer-button-wrap">
        <button @click.prevent="startTimer" v-if="(!timerRunning || timerPaused) && !timerDone">
            {{ timerRunning || timerPaused ? 'Resume' : 'Start' }}
        </button>
        <button @click.prevent="stopTimer" v-if="timerDone">Stop</button>
        <button @click.prevent="pauseTimer" v-if="timerRunning">Pause</button>
        <button @click.prevent="resetTimer" v-if="remainingTime <= totalTime">Reset</button>
        </div>
        <audio ref="alarmSound" :src="alarmSound" loop></audio>

        <p>Count: {{ this.totalUsed }}</p>

        <button @click="resetCount">Reset Count</button>
    </div>
</template>

<script>
export default {
  name: 'App',
  props: ['title', 'givenTime', 'cookieID', 'resetCount'],
  data() {
    return {
      timerRunning:false,
      timerPaused: false,
      timerDone: false,
      totalTime: this.givenTime,
      remainingTime: this.givenTime,
      alarmSound: require("@/assets/mp3/alarm.mp3"),
      totalUsed: 0,
      cookieData: null
    }
  },
  computed: {
    formatTime() {
      const minutes = Math.floor(this.remainingTime  / 60)
      const seconds = this.remainingTime % 60
      return `${String(minutes).padStart(2, '0')}:${String(seconds).padStart(2, '0')}`
    },
  },
  mounted() {
    this.getCookieData();
  },
  methods: {
    startTimer() {
      if (!this.timerRunning || this.timerPaused) {
        this.stopAlarmSound();
        this.timerRunning = true;
        this.timerPaused = false;
        this.interval = setInterval(() => {
          if (this.remainingTime > 0) {
            this.remainingTime -= 1;
          } else {
            // Clear Timer Interval
            clearInterval(this.interval)
            // Reset Data Value
            this.resetDataVal()
            this.timerDone = true
            // Play Alarm
            this.playAlarmSound()
            // Save to storage
            this.save()
          }
        }, 1000);
      }
    },
    pauseTimer() {
      this.stopAlarmSound()
      if (this.timerRunning) {
        clearInterval(this.interval);
        this.timerRunning = false;
        this.timerPaused = true;
      } else if (this.timerPaused) {
        this.startTimer();
        this.timerPaused = false;
      }
    },
    resetTimer() {
      clearInterval(this.interval);
      // Reset Data Value
      this.resetDataVal()
      this.timerDone = false
      // Stop Alarm
      this.stopAlarmSound()
    },
    stopTimer() {
      clearInterval(this.interval);
      // Reset Data Value
      this.resetDataVal()
      this.timerDone = false
      // Stop Alarm
      this.stopAlarmSound()
    },
    resetDataVal() {
        this.timerRunning = false;
        this.timerPaused = false;
        this.totalTime = this.givenTime
        this.remainingTime = this.givenTime
    },
    playAlarmSound() {
      this.$refs.alarmSound.play()
    },
    stopAlarmSound() {
      this.$refs.alarmSound.pause()
      this.$refs.alarmSound.currentTime = 0
    },
    save() {
        this.totalUsed++
        localStorage.setItem(this.cookieID, JSON.stringify({ totalUsed: this.totalUsed }));
    },
    getCookieData() {
      // Get data from local storage
      const storedData = localStorage.getItem(this.cookieID);

      // Check if data exists in local storage
      if (storedData) {
        // Parse the data (assuming it was stored as a JSON string)
        this.cookieData = JSON.parse(storedData);
        this.totalUsed = this.cookieData.totalUsed
      } else {
        // Handle the case where no data is found
        this.cookieData = null;
      }
    },
    resetCount() {
        this.totalUsed = 0
        localStorage.setItem(this.cookieID, JSON.stringify({ totalUsed: 0 }));
    }
  }
}
</script>