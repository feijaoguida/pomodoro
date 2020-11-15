<template>
  <v-card class="mt-8">
    <v-tabs @change="changeCurrentTimer" v-model="currentTimer" grow>
      <v-tab v-for="timer in timers" :key="timer.name" :disabled="isRunning">
        {{ timer.name }}
      </v-tab>
    </v-tabs>
    <v-card class="pa-5 d-flex flex-column justify-center align-center" flat>
      <h1 class="timer">{{ this.displayMinutes }}:{{ this.displaySeconds }}</h1>
      <div class="button-group">
        <v-btn @click="start" color="primary" :disabled="isRunning">
          <v-icon small left> mdi-play-speed </v-icon>
          Iniciar</v-btn
        >
        <v-btn @click="stop" color="error" :disabled="!isRunning">
          <v-icon small left> mdi-timer-off-outline </v-icon>
          Pausar</v-btn
        >
        <v-btn
          @click="reset(timers[currentTimer].minutes)"
          :disabled="isRunning"
        >
          <v-icon small left> mdi-restart </v-icon>
          Reiniciar</v-btn
        >
      </div>
    </v-card>
    <SettingDialog
      :dialog="dialog"
      :closeDialog="closeDialog"
      :save="save"
      :timers="timers"
    />
  </v-card>
</template>

<script>
import SettingDialog from "./SettingsDialog.vue";

export default {
  components: {
    SettingDialog,
  },
  props: {
    dialog: {
      type: Boolean,
      required: true,
    },
    closeDialog: {
      type: Function,
      required: true,
    },
  },
  data() {
    return {
      isRunning: false,
      timerInstance: null,
      totalSeconds: 25 * 60,
      currentTimer: 0,
      timers: [
        {
          name: "Pomodoro",
          minutes: 25,
        },
        {
          name: "Pausa Curta",
          minutes: 5,
        },
        {
          name: "Pausa Longa",
          minutes: 10,
        },
      ],
    };
  },
  computed: {
    displayMinutes() {
      const minutes = Math.floor(this.totalSeconds / 60);
      return this.formatTime(minutes);
    },
    displaySeconds() {
      const seconds = this.totalSeconds % 60;
      return this.formatTime(seconds);
    },
  },
  methods: {
    formatTime(time) {
      if (time < 10) {
        return "0" + time;
      }
      return time.toString();
    },
    start() {
      //this.stop();
      if (!this.isRunning) {
        this.timerInstance = setInterval(() => {
          if (this.totalSeconds <= 0) {
            this.isRunning = false;
            return;
          }
          this.totalSeconds -= 1;
        }, 1000);
        this.isRunning = true;
      }
    },
    stop() {
      this.isRunning = false;
      clearInterval(this.timerInstance);
    },
    reset(minutes) {
      this.stop();
      this.totalSeconds = minutes * 60;
    },
    changeCurrentTimer(num) {
      this.currentTimer = num;
      this.reset(this.timers[num].minutes);
    },
    save(updatedTimers) {
      this.timers = this.timers.map((timer, i) => {
        return { ...timer, minutes: parseInt(updatedTimers[i]) };
      });
      this.closeDialog();
    },
  },
};
</script>

<style lang="sass" scoped>
.v-card
  width: 600px
.v-btn
  margin: 0 3px
.timer
  font-size: 80px
  font-weight: 400
  text-align: center
</style>
