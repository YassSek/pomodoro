<template>
  <div id="upperBtn">
    <button id="pomodoro" @click="timerChange(1500)">Pomodoro</button>

    <button id="shBreak" @click="timerChange(300)">ShortBreak</button>

    <button id="lgBreak" @click="timerChange(600)">LongBreak</button>
  </div>

  <div class="base-timer">
    <svg
      class="timer__svg"
      viewBox="0 0 100 100"
      xmlns="http://www.w3.org/2000/svg"
    >
      <g class="timer__circle">
        <circle class="timer__path-elapsed" cx="50" cy="50" r="45" />
        <path
          v-bind:class="{ break: isOnBreak, work: !isOnBreak }"
          :stroke-dasharray="circleDasharray"
          d="
            M 50 50 
            m-45, 0
            a 45,45 0 1,0 90,0
            a 45,45 0 1,0 -90,0
          "
        ></path>
      </g>
    </svg>
    <span class="timer__label">{{ minutes }} : {{ seconds }}</span>
  </div>

  <div id="lowerBtn">
    <button id="start" v-if="!timerState" @click="timerStart">Start</button>

    <button id="pause" v-if="timerState" @click="timerPause">Pause</button>

    <button id="reset" v-if="resetBtn" @click="timerReset">Reset</button>
  </div>
</template>

<script>
const TIME_MAX = 1500;
const alarm = new Audio(
  "https://soundbible.com/mp3/analog-watch-alarm_daniel-simion.mp3"
);

export default {
  data: () => ({
    totalTime: 1500,
    timePassed: 0,
    resetBtn: false,
    timerState: null,
    isOnBreak: false,
    currentStep: 0,
    step: [
      { name: "Pomodoro", time: 1500 },
      { name: "Short Break", time: 300 },
      { name: "Pomodoro", time: 1500 },
      { name: "Long break", time: 600 }
    ]
  }),
  computed: {
    minutes: function() {
      const minutes = Math.floor(this.totalTime / 60);
      return this.padTime(minutes);
    },
    seconds: function() {
      const seconds = this.totalTime - this.minutes * 60;
      return this.padTime(seconds);
    },
    circleDasharray() {
      return `${(this.timeFraction * 283).toFixed(0)} 283`;
    },
    timeFraction() {
      const fraction = this.totalTime / TIME_MAX;
      return fraction - (1 / this.totalTime) * (1 - fraction);
    }
  },
  methods: {
    timerStart: function() {
      this.resetBtn = true;
      this.timerState = setInterval(() => {
        this.timerCount();
        if (this.totalTime <= 0) {
          alarm.play();
          this.currentStep += 1;
          this.timerChange(this.step[this.currentStep].time);
          return;
        }
        if (this.currentStep > 3) {
          this.timerReset();
        }
      }, 1000);
    },
    timerPause: function() {
      clearInterval(this.timerState), (this.timerState = null);
      this.resetBtn = true;
    },
    timerChange: function(minutes) {
      if (minutes == this.step[3].time) {
        this.isOnBreak = true;
        this.currentStep = 3;
      } else if (minutes == this.step[1].time) {
        this.isOnBreak = true;
        this.currentStep = 1;
      } else {
        this.isOnBreak = false;
      }
      this.totalTime = minutes;
      this.timePassed = 0;
      clearInterval(this.timerState), (this.timerState = null);
      this.timerStart();
    },
    timerReset: function() {
      this.currentStep = 0;
      this.totalTime = 1500;
      this.timePassed = 0;
      clearInterval(this.timerState);
      this.timerState = null;
      this.resetBtn = false;
      this.isOnBreak = false;
    },
    timerCount: function() {
      this.totalTime -= 1;
      this.timePassed += 1;
    },
    padTime: function(time) {
      return (time < 10 ? "0" : "") + time;
    }
  }
};
</script>

<style>
template {
  width: 100%;
  height: 100%;
}
.base-timer {
  background: linear-gradient(
    135deg,
    #212230 0%,
    #0a0b14 100%
  ); /* W3C, IE10+, FF16+, Chrome26+, Opera12+, Safari7+ */

  box-shadow: 5px 5px 5px 0px hsl(235, 33%, 6%),
    inset 4px 4px 15px 0px hsl(236, 18%, 16%), 5px 5px 15px 5px rgba(0, 0, 0, 0);
  border-radius: 50%;
  position: relative;
  width: 600px;
  height: 600px;
  margin-left: 30%;
  margin-top: 40px;
}

.timer__circle {
  fill: none;
  stroke: none;
}

.timer__path-elapsed {
  stroke-width: 2px;
  stroke: hsl(236, 18%, 16%);
}
.timer__label {
  position: absolute;
  margin-top: 40%;
  left: 30%;
  font-size: 80px;
  color: #fff;
}

.work {
  stroke-width: 2px;
  stroke-linecap: round;
  transform: rotate(90deg);
  transform-origin: center;
  transition: 1s linear all;
  stroke: #ff6961;
}

.break {
  stroke-width: 2px;
  stroke-linecap: round;
  transform: rotate(90deg);
  transform-origin: center;
  transition: 1s linear all;
  stroke: #77dd77;
}

.timer__svg {
  transform: scaleX(1);
}

#lowerBtn {
  display: relative;
  justify-content: center;
  margin-left: 36%;
  margin-top: 50px;
}
#lowerBtn button {
  width: 130px;
  height: 60px;
  background-color: hsl(235, 33%, 6%);
  color: #fff;
  border: rgb(241, 161, 177) 0.5px solid;
  border-radius: 40px;
  font-size: 3ch;
}

#upperBtn {
  width: 500px;
  height: 80px;
  display: relative;
  border: rgb(241, 161, 177) 1px solid;
  margin-left: 32%;
  margin-top: 20px;

  display: inline-block;
  border-radius: 40px;

  text-align: center;
  background-color: hsl(235, 33%, 6%);
}

#upperBtn button {
  margin: 15px;
  padding-bottom: 10px;
  font-size: 3ch;
  width: 130px;
  height: 60px;

  background-color: hsl(235, 33%, 6%);
  color: #fff;
  border: transparent;
  border-radius: 40px;
}

/* Small devices (portrait tablets and large phones, 600px and up) */
@media only screen and (max-width: 600px) {
  .timer__label {
    font-size: 45px;
  }
  .base-timer {
    margin-left: 40px;
    width: 300px;
    height: 300px;
  }
  #upperBtn {
    width: 300px;
    height: 60px;
    margin-left: 30px;
  }
  #upperBtn button {
    margin: 0px;
    padding-bottom: 0px;
    font-size: 1.5ch;
    width: 90px;
    height: 60px;
  }
  #lowerBtn {
    margin-left: 120px;
  }
}

/* Medium devices (landscape tablets, 768px and up) */
@media only screen and (min-width: 768px) and (max-width: 992px) {
  .base-timer {
    margin-left: 70px;
    width: 600px;
    height: 600px;
  }

  #upperBtn {
    width: 500px;
    height: 80px;
    margin-left: 100px;
  }
  #lowerBtn {
    margin-left: 250px;
  }
}
</style>
