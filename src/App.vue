<template>
  <div id="app">
    <span class="game-info">
      Уровень сложности: {{ currentLevel }}
      <br />
      Раунд: {{ round }}
    </span>
    <Pie
      ref="pie"
      :sequence="sequence"
      :speed="level"
      :onWin="handleWin"
      :onLose="handleLose"
    />

    <button @click="handleOnStartClick" class="start-button">НАЧАТЬ</button>

    <div>
      <button @click="handleOnEasyClick" class="start-button">ЛЕГКИЙ</button>
      <button @click="handleOnNormalClick" class="start-button">СРЕДНИЙ</button>
      <button @click="handleOnHardClick" class="start-button">ТЯЖЕЛЫЙ</button>
    </div>
  </div>
</template>

<script>
import Pie from "./components/Pie.vue";

const colors = ["red", "blue", "yellow", "green"];

export default {
  name: "App",
  data() {
    return {
      level: 0,
      round: 0,
      sequence: [],
    };
  },
  computed: {
    currentLevel: function () {
      switch (this.level) {
        case 1500:
          return "Легкий";
        case 1000:
          return "Средний";
        case 400:
          return "Тяжелый";
        default:
          return "Легкий";
      }
    },
  },
  methods: {
    handleOnStartClick() {
      this.startLevel(1500);
    },

    handleOnEasyClick() {
      this.startLevel(1500);
    },

    handleOnNormalClick() {
      this.startLevel(1000);
    },

    handleOnHardClick() {
      this.startLevel(400);
    },

    startLevel(level) {
      this.round = 0;
      this.level = level;
      this.$set(this.sequence, 'length', 0) 
      this.generateSequence();
      this.$refs.pie.start();
    },

    generateSequence() {
      this.sequence.push(colors[Math.floor(Math.random() * colors.length)]);
    },

    handleWin() {
      this.round++;
      const timeout = setTimeout(() => {
        this.generateSequence();
        this.$refs.pie.start();
        clearTimeout(timeout);
      }, 500);
    },

    handleLose() {
      alert("Вы проиграли");
      this.round = 0
      this.$set(this.sequence, 'length', 0) 
    },
  },
  components: {
    Pie,
  },
};
</script>

<style lang="scss">
#app {
  display: flex;
  flex-direction: column;
  align-items: center;
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 30px;
}
body{
  background-color: #fff;
}

.game-info {
  align-self: flex-start;
  text-align: left;
  padding: 24px;
  font-weight: 600;
  font-size: 24px;
}

.start-button {
  color: #fff;
  margin-top: 24px;
  padding: 10px;
  background: #6DABE8;
  border-radius: 10%;
}
</style>
