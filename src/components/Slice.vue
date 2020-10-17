<template>
    <div
      class="slice"
      @click="handleOnClick"
      v-bind:style="[{ backgroundColor: color, opacity: opacity }, overrideStyle]"
    />
</template>

<script>
export default {
  data(){
    return {
      opacity: 1
    }
  },
  props: {
    sound: String,
    speed: Number,
    color: String,
    overrideStyle: Object,
    onClick: Function,
  },

  methods: {
    blink() {
      this.playSound()
      this.opacity = 0.5

      const timeout = setTimeout(() => {
        this.opacity = 1
        clearTimeout(timeout)
      }, this.speed)
    },
    playSound () {
      var audio = new Audio(this.sound);
      audio.play();
    },
    handleOnClick() {
      this.blink();
      this.onClick(this.color);
    },
  },
};
</script>

<style scoped>
.slice {
  position: relative;
  width: 200px;
  height: 200px;
}
</style>