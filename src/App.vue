<template>
  <div id="app">
    <div class="wrapper">
      <div class="left">
        <div class="up">
          <div
              class="blue"
              :class="{blueOpacity: opacityValue.blueOpacity}"
              @click="opacityOnClick('blueOpacity')"
          ></div>
          <div
              class="red"
              :class="{redOpacity: opacityValue.redOpacity}"
              @click="opacityOnClick('redOpacity')"
          ></div>
        </div>
        <div class="down">
          <div
              class="yellow"
              :class="{yellowOpacity: opacityValue.yellowOpacity}"
              @click="opacityOnClick('yellowOpacity')"
          ></div>
          <div
              class="green"
              :class="{greenOpacity: opacityValue.greenOpacity}"
              @click="opacityOnClick('greenOpacity')"
          ></div>
        </div>
      </div>
      <div class="right">
        <h2 class="lose" v-if="gameOver">Вы проиграли!</h2>
        <h1>Score: {{score}}</h1>
        <button v-if="!start" @click="startClick">Start</button>
        <hr />
        <h2>Выберите сложность</h2>
        <div class="selectLevel">
          <div>
            <input type="radio" id="easy" name="level" value="easyLevel" v-model="currentLevel">
            <label for="easy">Легкий</label>
          </div>
          <div>
            <input type="radio" id="medium" name="level" value="mediumLevel" v-model="currentLevel">
            <label for="medium">Средний</label>
          </div>
          <div>
            <input type="radio" id="hard" name="level" value="hardLevel" v-model="currentLevel">
            <label for="hard">Сложный</label>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "SimonGame",
  data() {
    return {
      score: 0,
      gameArray: [],
      playerArray: [],
      start: false,
      gameOver: false,
      baseParamColor: {
        1: "blueOpacity",
        2: "redOpacity",
        3: "yellowOpacity",
        4: "greenOpacity"
      },
      currentColor: "",
      opacityValue: {
        blueOpacity: false,
        redOpacity: false,
        yellowOpacity: false,
        greenOpacity: false,
      },
      levels: {
        easyLevel: 1500,
        mediumLevel: 1000,
        hardLevel: 400
      },
      currentLevel: "easyLevel",
      isClickable: false
    }
  },
  methods: {
    startClick() {
      this.start = true
      this.gameOver = false
      this.startGameBot(this.getLevel)
    },
    randomInteger(min, max) {
      const rand = min - 0.5 + Math.random() * (max - min + 1)
      return Math.round(rand)
    },
    startGameBot(level) {
      this.gameArray.push(this.randomInteger(1, 4))
      this.gameArray.forEach((el, index) => {
        setTimeout(() => {
          let color = this.baseParamColor[el]
          this.opacityValue[color] = true
          setTimeout(() => {
            this.opacityValue[color] = false
            this.audioOnClick(color)
          }, 300)
        }, level * (index + 1)) 
      })
    },
    opacityOnClick(color) { 
      this.opacityValue[color] = true
      this.isClickable = true
      this.currentColor = color
      setTimeout(() => {
        this.opacityValue[color] = false
        this.isClickable = false
        this.audioOnClick(color)
      }, 200)
    },
    getKeyByValue(object, value) { 
      return Object.keys(object).find(key => object[key] === value);
    },
    checkUserArray() {
      this.playerArray.forEach((el, index) => {
        if (el !== this.gameArray[index]) {
          this.gameOver = true
          return
        }
      })

      if (this.playerArray.length === this.gameArray.length && !this.gameOver) {
        this.playerArray.length = 0
        this.score++
        this.startClick()
      }
    },
    audioOnClick(color) { 
      let audio
      switch (color) {
        case("blueOpacity"):
          audio = new Audio("https://s3.amazonaws.com/freecodecamp/simonSound1.mp3")
          audio.play()
          break
        case ("redOpacity"):
          audio = new Audio("https://s3.amazonaws.com/freecodecamp/simonSound2.mp3")
          audio.play()
          break
        case ("yellowOpacity"):
          audio = new Audio("https://s3.amazonaws.com/freecodecamp/simonSound3.mp3")
          audio.play()
          break
        case ("greenOpacity"):
          audio = new Audio("https://s3.amazonaws.com/freecodecamp/simonSound4.mp3")
          audio.play()
          break
      }
    }
  },
  watch: {
    currentLevel() {
      this.start = false
      this.score = 0
      this.gameArray.length = 0
      this.playerArray.length = 0
    },
    isClickable() {
      if (this.isClickable) {
        let value = this.getKeyByValue(this.baseParamColor, this.currentColor)
        this.playerArray.push(Number(value))
        this.checkUserArray()
      }
    },
    gameOver() {
      if (this.gameOver) {
        this.start = false
        this.gameArray.length = 0
        this.playerArray.length = 0
        this.score = 0
      }
    }
  },
  computed: {
    getLevel() {
      return this.levels[this.currentLevel]
    },

  }
}
</script>

<style lang="scss" scoped>
  @mixin base-params {
    width: 200px;
    height: 200px;
    opacity: .1;
    cursor: pointer;
    border: 0 solid;
  }

  @mixin flex-row {
    display: flex;
    flex-direction: row;
  }

  .wrapper {
    margin: 40px 0;
    display: flex;
    flex-direction: row;
    justify-content: space-around;
    .left {
      width: 400px;
      height: 400px;
      border-radius: 50%;
      overflow: hidden;
      .up {
        @include flex-row;

        .blue {
          background-color: blue;
          @include base-params;
        }

        .blueOpacity {
          opacity: 1;
        }

        .red {
          background-color: red;
          @include base-params;
        }

        .redOpacity {
          opacity: 1;
        }

      }

      .down {
        @include flex-row;

        .yellow {
          background-color: yellow;
          @include base-params;
        }

        .yellowOpacity {
          opacity: 1;
        }

        .green {
          background-color: green;
          @include base-params;
        }

        .greenOpacity {
          opacity: 1;
        }
      }

    }

    .right {
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;

      .lose {
        color: red;
        font-size: 30px;
      }

      .levelSelect {
        display: flex;
      }

      h1 {
        font-size: 50px;
      }

      button {
        font-size: 20px;
        width: 200px;
        height: 50px;
        cursor: pointer;
        border-radius: 20px;
        background-color: rgb(207 250 254);
      }
    }

  }

</style>
