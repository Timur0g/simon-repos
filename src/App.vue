<template>
  <div id="app">
    <div class="colors" :class="{ spin : spin }">
      <input
        type="button"
        v-for="button in buttons"
        v-bind:key="button.id"
        class="color"
        :class="button.activeClass ? `${button.name}_active` : `${button.name}`"
        @click="boop(button.id, true)"
        :disabled="disabledButtons"
      />
    </div>
    <div class="info">
      <h3>{{ result }}</h3>
      <h1>Вы на {{ level }} уровне</h1>
      <input
        id="start"
        type="button"
        :disabled="disabledButtonStart"
        @click="start"
        :value="buttonStart"
      />

      <div class="options">
        <input
          type="range"
          min="400"
          max="1500"
          v-model="turn"
          :disabled="disableRange"
        />
        <div class="complexity">
          <span v-if="this.turn <= 633"><b>HARD</b></span>
          <span v-else>HARD</span>

          <span v-if="this.turn >= 634 && this.turn <= 1266"
            ><b>MEDIUM</b></span
          >
          <span v-else>MEDIUM</span>

          <span v-if="this.turn >= 1267"><b>EASY</b></span>
          <span v-else>EASY</span>
        </div>
        {{ turn }}

        <button id="superButton" @click="youSpinMeRghtRound">{{ superButtonText }}</button>
      </div>
    </div>
  </div>
</template>

<script>

export default {
  name: "App",
  data() {
    return {
      buttons: [
        { id: 1, name: "green", activeClass: false },
        { id: 2, name: "blue", activeClass: false },
        { id: 3, name: "red", activeClass: false },
        { id: 4, name: "yellow", activeClass: false },
      ],
      disabledButtons: false,
      level: 0,
      randomArray: [],
      userSelection: [],

      buttonStart: "START",
      disabledButtonStart: false,
      turn: 1900 / 2,

      disableRange: false,

      result: "Начните игру",
      superButtonText: 'SUPER HARD',

      spin: false
    };
  },
  methods: {
    youSpinMeRghtRound() {
      this.spin = !this.spin
      this.spin ? this.superButtonText = 'STOP' : this.superButtonText = 'SUPER HARD'
    },
    comprasion(nomber) {
      this.userSelection.push(nomber);
      if (this.randomArray.length > 0) {
        const result = this.userSelection.map((item, index) =>
          item == this.randomArray[index] ? true : false
        );
        if (result.some((item) => item == false)) {
          (this.randomArray = this.userSelection = []), (this.level = 0);
          this.disabledButtonStart = false;
          this.buttonStart = "RESTART";
          this.disabledButtons = true;
          this.result = "Вы проиграли. Попробуйте ещё раз";
        } else if (this.userSelection.length === this.randomArray.length) {
          this.level++;
          this.result = "Отлично! Продолжайте в том же духе!";
          setTimeout(() => {
            this.result = "";
            this.start();
          }, 2000);
        }
      }
    },
    boop(nomber, comparisonEnable = false) {
      const audio = new Audio(require(`./assets/${nomber}.ogg`));
      const index = this.buttons.findIndex((el) => el.id === nomber);
      audio.play();
      this.buttons[index].activeClass = true;
      if (comparisonEnable) {
        this.comprasion(nomber);
      }
      setTimeout(() => {
        this.buttons[index].activeClass = false;
      }, 200);
    },

    start() {
      let turnCopy = this.turn;
      let turnCopyOldValue = turnCopy;
      this.result = "";
      const _this = this;
      this.userSelection = [];
      this.disabledButtonStart = true;
      this.disabledButtons = true;
      this.disableRange = true;

      this.randomArray.push(Math.floor(Math.random() * 4) + 1);
      this.randomArray.forEach((item, index, array) => {
        new Promise((resolve) => {
          setTimeout(() => {
            if (++index == array.length) {
              resolve();
            }
            _this.boop(item);
          }, turnCopy);
          turnCopy = Number(turnCopy) + Number(turnCopyOldValue);
        }).then(() => {
          turnCopy = turnCopyOldValue;
          this.disableRange = false;
          this.disabledButtons = false;
        });
      });
    },
  },
};
</script>

<style lang="scss">

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
  width: 100%;
  display: flex;
  justify-content: center;
  flex-wrap: wrap;
}

</style>
