<template>
  <div class="paper">
    <div class="playground">
      <div>
        <div class="pull-right info">
          <h1>Math Game!</h1>
          <p>Find the right result.</p>
        </div>
      </div>
      <div v-if="isVisible" class="results">
        <div :class="animation" :style="{ color: activeColor}" class="animated task">
          {{formular}} =
          <span>{{sResult}}</span>
        </div>
        <div
          class="result"
          v-for="r in results"
          v-bind:key="r.key"
          v-bind:style="{'order':r.order}"
          v-on:click="click(r)"
        >{{r.result}} = x</div>
      </div>
    </div>
  </div>
</template>

<script>
import { store } from "./storePlugin";
export default {
  name: "math",
  data: function() {
    return {
      isVisible: false,
      key: 0,
      formular: "0+0",
      sResult: "x",
      result: 0,
      steps: 0,
      level: 1,
      animation: "",
      activeColor: ""
    };
  },
  created: function() {
    this.results = [];
    this.getChallenge();
  },
  methods: {
    start: function() {},
    click: function(item) {
      this.sResult = item.result;
      this.animation = "shake";
      this.activeColor = "#f00";
      if (this.result == item.result) {
        this.animation = "tada";
        this.activeColor = "";
        this.steps++;
        if (this.steps > 1) {
          this.level++;
          console.log(this.level);
          this.steps = 0;
        }
        this.getChallenge();
      } else {
        this.sResult += " f";
      }
      setTimeout(() => {
        this.animation = "";
      }, 500);
    },

    getRandom: function(min, max, level) {
      return Math.round(Math.random() * (max * level - min) + min);
    },

    isOdd: function(num) {
      return num % 2;
    },

    getChallenge: function() {
      this.isVisible = false;
      this.sResult = "x";
      this.results = [];
      var sign = ["+", "-", "*"];
      var multi = this.getRandom(3, 2 + this.level, 1);
      if (!this.isOdd(multi)) {
        multi++;
      }
      var formular = "";
      var lastSign = "+";
      for (var i = 0; i < multi; i++) {
        if (!this.isOdd(i)) {
          formular +=
            " " + this.getRandom(1, 9, lastSign === "*" ? 1 : multi / 2);
        } else {
          lastSign = sign[this.getRandom(0, 2, 1)];
          formular += " " + lastSign;
        }
      }

      var numberPattern = /\d+/g;
      var numbers = formular.match(numberPattern);

      /* get lowes nummber */
      var smalles = numbers.sort();

      var result = eval(formular);
      this.formular = formular;
      this.result = result;

      this.results.push({ result: result, order: 0, key: this.key++ });
      var alt = 0;
      for (let index = 0; index < 2; index++) {
        alt = eval(result + sign[index] + smalles[0]);
        this.results.push({ result: alt, order: 0, key: this.key++ });
      }
      for (let index = 0; index < 2; index++) {
        alt = eval(result + sign[index] + smalles[1]);
        this.results.push({ result: alt, order: 0, key: this.key++ });
      }

      if (store.appMode == "Pro") {
        for (let index = 0; index < 2; index++) {
          alt = eval(result + sign[index] + 1);
          this.results.push({ result: alt, order: 0 });
        }
      }

      this.results.forEach(item => {
        let randomPos = Math.floor(Math.random() * 5);
        item.order = randomPos;
      });

      this.isVisible = true;
    },

    checkLevel: function() {
      var level = this.level,
        step = this.levelStep;
      if (this.successful + 1 >= step * level) {
        level = this.level + 1;
      }
      return level;
    }
  }
};
</script>

<style  lang="stylus">
@import 'assets/app';
</style>

 
 
<style>
@keyframes flipIn {
  from {
    transform: rotate3d(0, 1, 0, 180deg);
    animation-timing-function: ease-out;
  }
  to {
    transform: rotate3d(0, 1, 0, 0deg);
    animation-timing-function: ease-in;
  }
}
@keyframes flipOut {
  from {
    transform: rotate3d(0, 1, 0, 180deg);
    animation-timing-function: ease-out;
  }
  to {
    transform: rotate3d(0, 1, 0, 0deg);
    animation-timing-function: ease-in;
  }
}

@keyframes tada {
  from {
    transform: scale3d(1, 1, 1);
  }

  10%,
  20% {
    transform: scale3d(0.9, 0.9, 0.9) rotate3d(0, 0, 1, -3deg);
  }

  30%,
  50%,
  70%,
  90% {
    transform: scale3d(1.1, 1.1, 1.1) rotate3d(0, 0, 1, 3deg);
  }

  40%,
  60%,
  80% {
    transform: scale3d(1.1, 1.1, 1.1) rotate3d(0, 0, 1, -3deg);
  }

  to {
    transform: scale3d(1, 1, 1);
  }
}

@keyframes shake {
  from,
  to {
    transform: translate3d(0, 0, 0);
  }

  10%,
  30%,
  50%,
  70%,
  90% {
    transform: translate3d(-10px, 0, 0);
  }

  20%,
  40%,
  60%,
  80% {
    transform: translate3d(10px, 0, 0);
  }
}

.animated.shake {
  animation-duration: 1s;
  animation-name: shake;
}

.animated.tada {
  animation-duration: 1s;
  animation-name: tada;
}

.animated {
  backface-visibility: visible;
  animation-duration: 0.5s;
  animation-fill-mode: both;
}
</style>