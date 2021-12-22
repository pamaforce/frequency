<template>
  <div id="app">
    <div class="loading-class" v-show="page === -1">
      <div class="content-class">
        <vm-progress :percentage="percent"></vm-progress>
      </div>
    </div>
    <template v-for="(item, i) in imgs">
      <transition name="slide-fade" :key="i">
        <div class="container" v-show="page === i">
          <img :src="item" alt="加载中……(#^.^#)" />
          <div class="startBtn" @click="page++" v-show="page === 0"></div>
          <div class="answerA" @click="choose(2)" v-show="page > 0"></div>
          <div class="answerB" @click="choose(0)" v-show="page > 0"></div>
          <div class="answerC" @click="choose(1)" v-show="page > 0"></div>
        </div>
      </transition>
    </template>
    <div class="container" v-show="page === 16">
      <img
        :src="require('./assets/' + result + '.png')"
        alt="加载中……(#^.^#)"
      />
    </div>
    <my-music />
  </div>
</template>

<script>
const { aplus_queue } = window;
aplus_queue.push({
  action: "aplus.sendPV",
  arguments: [
    {
      is_auto: false,
    },
  ],
});
import myMusic from "./music.vue";
export default {
  name: "App",
  components: {
    myMusic,
  },
  data() {
    return {
      page: -1,
      percent: 0,
      count: 0,
      ac: false,
      result: "Hz",
      answers: [],
      obj: {},
      imgs: [
        require("./assets/q0_min.png"),
        require("./assets/q1_min.png"),
        require("./assets/q2_min.png"),
        require("./assets/q3_min.png"),
        require("./assets/q4_min.png"),
        require("./assets/q5_min.png"),
        require("./assets/q6_min.png"),
        require("./assets/q7_min.png"),
        require("./assets/q8_min.png"),
        require("./assets/q9_min.png"),
        require("./assets/q10_min.png"),
        require("./assets/q11_min.png"),
        require("./assets/q12_min.png"),
        require("./assets/q13_min.png"),
        require("./assets/q14_min.png"),
        require("./assets/q15_min.png"),
      ],
    };
  },
  created: function () {
    this.preload();
  },
  watch: {
    count: function (val) {
      if (val === this.imgs.length) {
        this.ac = true;
        setTimeout(() => {
          this.page = 0;
        }, 100);
      }
    },
  },
  methods: {
    choose(item) {
      this.answers[this.page - 1] = item;
      if (this.page === 15) {
        this.result = this.getResult();
        this.obj["Result"] = this.result;
        aplus_queue.push({
          action: "aplus.record",
          arguments: ["get_result", "EXP", this.obj],
        });
        setTimeout(() => {
          this.page++;
        }, 0);
      } else {
        this.page++;
      }
    },
    getResult() {
      let calc = [0],
        count = 0,
        chars = ["B", "C", "A"],
        now = new Date();
      calc[1] = this.answers[0];
      this.obj["Question_1"] = chars[this.answers[0]];
      this.obj["eventParams"] =
        now.getFullYear() +
        "年" +
        (now.getMonth() + 1) +
        "月" +
        now.getDate() +
        "日 " +
        now.getHours() +
        ":" +
        now.getMinutes() +
        ":" +
        now.getSeconds();
      if (this.answers[0] === 1) count++;
      for (let i = 1; i < this.answers.length; i++) {
        this.obj["Question_" + (i + 1)] = chars[this.answers[i]];
        if (this.answers[i] === 1) count++;
        calc[i + 1] = this.answers[i] + calc[i];
      }
      if (calc[15] > 25) return "1895Hz";
      else if (calc[3] > 4 && calc[12] - calc[9] > 4) return "381Hz";
      else if (calc[6] - calc[3] > 4 && calc[12] - calc[9] > 4) return "1230Hz";
      else if (calc[9] - calc[6] > 4 && calc[15] - calc[12] > 4)
        return "1441Hz";
      else if (calc[3] > 5) return "145Hz";
      else if (calc[6] - calc[3] > 5) return "1516Hz";
      else if (calc[9] - calc[6] > 5) return "1089Hz";
      else if (calc[12] - calc[9] > 5) return "999Hz";
      else if (calc[15] - calc[12] > 5) return "1906Hz";
      else if (count > 10) return "Hz";
      else return "52Hz";
    },
    preload() {
      setTimeout(() => {
        if (!this.ac && this.page == -1) this.page = 0;
      }, 5000);
      for (let img of this.imgs) {
        let image = new Image();
        image.src = img;
        image.onload = () => {
          this.count++;
          this.percent = Math.floor((this.count / this.imgs.length) * 100);
        };
      }
    },
  },
};
</script>

<style>
#app {
  position: relative;
  text-align: center;
  overflow: hidden;
  padding-bottom: 0;
  padding-bottom: constant(safe-area-inset-bottom);
  padding-bottom: env(safe-area-inset-bottom);
}
.loading-class {
  height: 100vh;
  width: 100%;
  max-width: 400px;
  margin: 0 auto;
}
.content-class {
  width: 300px;
  position: relative;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}
.container {
  height: 100vh;
  width: 100vw;
  max-width: 400px;
  position: relative;
  margin: 0 auto;
}
.container img {
  position: absolute;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
}
.startBtn {
  position: absolute;
  left: 50%;
  transform: translateX(-50%);
  width: 190px;
  height: 100px;
  bottom: 12vh;
}
.answerA {
  position: absolute;
  left: 50%;
  transform: translateX(-50%);
  bottom: 31vh;
  width: 300px;
  height: 40px;
}
.answerB {
  position: absolute;
  left: 50%;
  transform: translateX(-50%);
  bottom: 22vh;
  width: 300px;
  height: 40px;
}
.answerC {
  position: absolute;
  left: 50%;
  transform: translateX(-50%);
  bottom: 13vh;
  width: 300px;
  height: 40px;
}
.slide-fade-enter-active {
  transition: all 0.3s ease;
}
.slide-fade-leave-active {
  transition: all 0.3s ease;
}
.slide-fade-enter,
.slide-fade-leave-to {
  opacity: 0.4;
}
</style>
