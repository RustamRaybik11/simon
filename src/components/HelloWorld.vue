<template>
<div>
  <div >
    <div class="game" :class="{fullSize: playing}">
      <div class="square" @click.prevent="playSound('https://s3.amazonaws.com/freecodecamp/simonSound1.mp3')" @click="clicked(1)" :class="{lit: isLit[1]}"></div>
      <div class="square" @click.prevent="playSound('https://s3.amazonaws.com/freecodecamp/simonSound2.mp3')" @click="clicked(2)" :class="{lit: isLit[2]}"></div>
      <div class="square" @click.prevent="playSound('https://s3.amazonaws.com/freecodecamp/simonSound3.mp3')" @click="clicked(3)" :class="{lit: isLit[3]}"></div>
      <div class="square" @click.prevent="playSound('https://s3.amazonaws.com/freecodecamp/simonSound4.mp3')" @click="clicked(4)" :class="{lit: isLit[4]}"></div>
      <div class="start" @click="startGame">{{ centerButton }}
        <span v-show="playing">{{ showScore }}</span>
      </div>
    </div>
  </div>
  <div class="row">
    <label class="label">*Тип документа</label><br><br>
    <div class="radBlock">
        <label class="label">
            <input class="input" type="radio" value="Пасспорт" v-model.trim="$v.mode.$model">Пасспорт
        </label>
    </div>
    <div class="radBlock">
        <label class="label">
            <input class="input" type="radio" value="Свидетельство о рождении">Свидетельство о рождении
        </label>
    </div>
    <div class="radBlock">
        <label class="label">
            <input class="input" type="radio" value="Вод. удостоверение">Вод. удостоверение
        </label>
    </div>

    <div class="error" v-if="!$v.formReg.document.required">{{reqText}}</div>
  </div>
  <div v-show="playing" class="bottom-bar">
    <p>Mode: </p>
    <button :class="{active: !strict}" @click="strict = false">Normal</button>
    <button :class="{active: strict}" @click="strict = true">Strict</button>
  </div>
</div>
</template>

<script>
export default{
  data() {
    return{
    centerButton: "START",
    playing: false,
    isClickable: false,
    isWon: false,
    isWrong: false,
    strict: false,
    mode: 'easy',
    speed: 0,
    score: 0,
    sequence: [],
    sequenceInterval: null,
    playerSequence: [],
    isLit: {
      1: false,
      2: false,
      3: false,
      4: false
    }
    }
  },
  computed: {
    showScore() {
      if (this.isWon) {
        return "Play Again?";
      }
      return "Score: " + this.score;
    },

  },
  watch: {
    // if strict changes in middle of game, reset it
    strict() {
      this.startGame();
    }
  },
  methods: {
    startGame() {
      this.playing = true;
      this.sequence = [];
      this.playerSequence = [];
      this.centerButton = "RESET";
      this.isWon = false;
      this.isWrong = false;
      this.score = 0;
      clearInterval(this.sequenceInterval);
      this.showSequence();
    },
    clicked(tile) {
      if (this.isClickable) {
        this.playSound(tile);
        this.lightUp(tile);
        this.playerSequence.push(tile);
        this.checkWinLose();
      }
    },
    checkWinLose() {
      // Проверка последовательности
      for (let i = 0; i < this.playerSequence.length; i++) {
        if (this.playerSequence[i] !== this.sequence[i]) {
          this.playerSequence = [];
          this.centerButton = "Wrong!";
          this.isWrong = true;
          setTimeout(() => {
            this.centerButton = "RESET";
            this.isWrong = false;
          }, 5000);
          this.showSequence(true);
        }
      }
      // Если все верно -
      if (this.playerSequence.length === this.sequence.length) {
        this.playerSequence = [];
        this.score++;
        this.showSequence();
      }
    },
    lightUp(tile) {
      this.isLit[tile] = true;
      setTimeout(() => {
        this.isLit[tile] = false;
      }, 300);
    },
    playSound (tile) {
      if(tile) {
        var audio = new Audio(tile);
        audio.play();
      }
    },
      showSequence(redo) {
        let currentIndex = 0;
        let speed = this.sequence.length === 0 ? 1000 : 700;
        this.isClickable = false;
        if (!redo) {
          // Не добавлять число за непрвельный ответ
          this.sequence.push(Math.floor(Math.random() * 4 + 1));
        }
        this.sequenceInterval = setInterval(() => {
          if (currentIndex >= this.sequence.length) {
            clearInterval(this.sequenceInterval);
            return (this.isClickable = true);
          }
          this.playSound();
          this.lightUp(this.sequence[currentIndex]);
          currentIndex++;
        }, speed);
    }
  }
}
</script>


<style lang="scss" scoped>
  $bg-color: #343434;
  $red: #AA2525;
  $white: #FFF;
  $yellow: #AAAA25;
  $green: #45AA25;
  $blue: #2525AA;

@mixin center-align {
  align-items: center;
  display: flex;
  justify-content: center;
}

body {
  @include center-align();
  background: $bg-color;
  font-family: 'Lato';
  font-weight: 900;
  font-size: 1.4em;
  height: 100vh;
  overflow: hidden;
}

.game {
  align-content: space-around;
  background: darken($bg-color, 8%);
  border-radius: 50%;
  display: flex;
  flex-wrap: wrap;
  height: 500px;
  justify-content: space-around;
  width: 500px;
  .start {
    @include center-align();
    align-content: center;
    flex-wrap: wrap;
    background: $white;
    border: 20px solid darken($bg-color, 5%);
    border-radius: 50%;
    cursor: pointer;
    position: absolute;
    height: 100px;
    left: 50%;
    top: 50%;
    user-select: none;
    width: 100px;
    span {
      font-size: .7em;
    }
  }
  .square {
    cursor: pointer;
    height: 45%;
    width: 45%;
    
    &:nth-of-type(1) {
      background: $green;
      border-radius: 100% 0 0 0;
      &.lit {
        background: lighten($green, 25%);
        
      }
    }
    &:nth-of-type(2) {
      background: $red;
      border-radius: 0 100% 0 0;
      &.lit {
        background: lighten($red, 25%);
      }
    }
    &:nth-of-type(3) {
      background: $yellow;
      border-radius: 0 0 0 100%;
      &.lit {
        background: lighten($yellow, 25%);
      }
    }
    &:nth-of-type(4) {
      background: $blue;
      border-radius: 0 0 100% 0;
      &.lit {
        background: lighten($blue, 25%);
      }
    }
  }
}

.bottom-bar {
  animation: moveup 1s ease-in forwards;
  background: darken($bg-color, 10%);
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 0.3em 0;
  position: absolute;
  bottom: 0;
  left: 0;
  width: 100%;
  
  button {
    background: lighten($bg-color, 10%);
    color: $white;
    border: none;
    border-radius: 5px;
    outline: none;
    font-size: .6em;
    padding: .4em;
    &.active {
      background: $green;
    }
    &:nth-of-type(1) {
      margin-right: .3em;
    }
  }
  p {
    color: $white;
    margin: 0;
    font-size: .6em;
    display: inline;
    padding-right: 1em;
  }
}

</style>