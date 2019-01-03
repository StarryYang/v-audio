<template>
  <div class="v-audio">
    <div class="stop" @click="stop" v-if="btns"></div>
    <div class="start" @click="play" v-if="!btns"></div>
    <div class="audio">
      <!-- 播放时间 -->
      <div class="curT">{{curT}}</div>
      <!-- 总进度条 -->
      <div class="bar">
        <input
          type="range"
          :disabled="total==''?true:false"
          min="0"
          max="100"
          v-model="curs"
          @input="playN"
        >
        <!-- 播放进度 -->
        <div class="curB" :style="{width:curs+'%'}"></div>
        <!-- 播放进度点 -->
        <div class="audioB" :style="{left:curs+'%'}"></div>
      </div>
      <!-- 播放总时长 -->
      <div class="total">{{total}}</div>
    </div>
    <audio id="myaudio" controls="controls" preload hidden :src="audios"></audio>
  </div>
</template>
<script>
export default {
  props: ["src"],
  data() {
    return {
      audio: "",
      curT: "00:00",
      btns: false,
      total: "",
      audios: "",
      curs: 0
    };
  },
  methods: {
    initA() {
      let audio = document.getElementById("myaudio");
      this.audio = audio;
      const v = this;
      audio.addEventListener("canplay", function() {
        v.getTime(v.audio);
      });
    },
    //结束拖动播放
    playN() {
      //获取当前的播放时间值
      var current = this.audio.duration * (this.curs / 100);
      this.audio.currentTime = current;
      this.btns = false;
      this.play();
    },
    //获取进度条
    getC() {
      // 滑动时修改当前时间值
      if (this.curs == 0) {
        this.curT = "00:00";
        return false;
      }
      if (this.curs == 100) {
        return (this.curT = this.total);
      }
      if (this.curs > 0 && this.curs < 100) {
        var current = this.audio.duration * (this.curs / 100);
        var minute = current / 60;
        var minutes = parseInt(minute);
        if (minutes < 10) {
          minutes = "0" + minutes;
        }
        //秒
        var second = current % 60;
        var seconds = Math.round(second);
        if (seconds < 10) {
          seconds = "0" + seconds;
        }
      }
      this.curT = minutes + ":" + seconds;
    },
    //获取进度条。播放时间，和播放进度
    getTime() {
      var minute = this.audio.duration / 60;
      var minutes = parseInt(minute);
      if (minutes < 10) {
        minutes = "0" + minutes;
      }
      //秒
      var second = this.audio.duration % 60;
      var seconds = Math.round(second);
      if (seconds < 10) {
        seconds = "0" + seconds;
      }
      this.total = minutes + ":" + seconds;
    },
    //视频停止按钮
    stop() {
      this.audio.pause();
      this.btns = !this.btns;
      if (this.timer != "") {
        clearInterval(this.timer);
      }
    },
    // 视频播放按钮
    play() {
      this.audio.play();
      this.btns = !this.btns;
      this.timer = setInterval(() => {
        this.curs = (this.audio.currentTime / this.audio.duration) * 100;
        this.getC();
        if (this.audio.currentTime == this.audio.duration) {
          clearInterval(this.timer);
          this.btns = false;
        }
      }, 800);
    }
  },
  mounted() {
    this.audios = this.src;
    this.initA();
  }
};
</script>
<style >
.v-audio {
  height: 40px;
  padding: 15px;
  background: #fff;
  position: relative;
  text-align: left;
}
.stop {
  width: 40px;
  height: 40px;
  background: url("./stop.png") center top / 100% 100% no-repeat;
  display: inline-block;
}
.start {
  width: 40px;
  height: 40px;
  background: url("./start.png") center top / 100% 100% no-repeat;
  display: inline-block;
}
.audio {
  position: absolute;
  left: 60px;
  top: 50%;
  width: 92%;
  height: 40px;
  line-height: 40px;
  transform: translateY(-50%);
}
.curT,
.total {
  font-size: 24px;
  color: #5a4000;
  position: absolute;
  left: 0px;
  bottom: 0;
  top: 50%;
  height: 36px;
  line-height: 36px;
  margin-top: -18px;
}
.total {
  left: auto;
  right: 6px;
}
.bar {
  width: 80%;
  height: 6px;
  border-radius: 3px;
  background: #cacaca;
  position: absolute;
  left: 90px;
  top: 50%;
  margin-top: -3px;
}
.bar input {
  width: 100%;
  position: absolute;
  top: 0;
  left: 0;
  height: 6px;
  z-index: 3;
  opacity: 0;
}
.curB {
  position: absolute;
  left: 0;
  top: 0;
  width: 20%;
  height: 6px;
  background: #ffaa00;
  border-radius: 3px;
}
.audioB {
  position: absolute;
  left: 20%;
  top: -11px;
  margin-left: -11px;
  width: 28px;
  height: 28px;
  border-radius: 50%;
  background: #ffaa00;
  z-index: 1;
}
</style>