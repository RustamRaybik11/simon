<template>
  <div class="pie">
    <Slice
      ref="red"
      :onClick="handleOnSliceClick"
      color="red"
      sound="https://s3.amazonaws.com/freecodecamp/simonSound1.mp3"
      :speed="speed / 2"
      class="top-left"
    />
    <Slice
      ref="blue"
      :onClick="handleOnSliceClick"
      color="blue"
      sound="https://s3.amazonaws.com/freecodecamp/simonSound2.mp3"
      :speed="speed / 2"
      class="top-right"
    />
    <Slice
      ref="yellow"
      :onClick="handleOnSliceClick"
      color="yellow"
      sound="https://s3.amazonaws.com/freecodecamp/simonSound3.mp3"
      :speed="speed / 2"
      class="bottom-left"
    />
    <Slice
      ref="green"
      :onClick="handleOnSliceClick"
      color="green"
      sound="https://s3.amazonaws.com/freecodecamp/simonSound4.mp3"
      :speed="speed / 2"
      class="bottom-right"
    />
  </div>
</template>

<script>
import Slice from "./Slice.vue";

export default {
  data () {
    return {
      input: []
    }
  },
  props: {
    speed: Number,
    sequence: Array,
    onWin: Function,
    onLose: Function
  },
  methods: {
    reset () {
      console.log('Reset');
      this.$set(this.input, 'length', 0) 
    },
    start() {
      console.log('Start',  this.sequence)
      this.reset()
      this.sequence.map((color, index) => {
        const timeout = setTimeout(() => {
          this.$refs[color].blink()
          clearTimeout(timeout)
        }, (index + 1) * this.speed);
      })
    },
    handleOnSliceClick(color) {
      if(color === this.sequence[this.input.length]){
        this.input.push(color)
        if(this.input.length === this.sequence.length){
          this.onWin()
        }
        return
      }

      this.onLose()
    },
  },
  components: {
    Slice,
  },
}
</script>


<style scoped>
.pie {
  position: relative;
  display: flex;
  flex-wrap: wrap;
  width: 400px;
  height: 400px;
  border-radius: 100%;
  border-width: 10px;
  background-color: black;
  border-color: black;
  border-style: solid;
}
.top-left {
  border-top-left-radius: 100%;
}
.top-right {
  border-top-right-radius: 100%;
}
.bottom-left {
  border-bottom-left-radius: 100%;
}
.bottom-right {
  border-bottom-right-radius: 100%;
}
</style>