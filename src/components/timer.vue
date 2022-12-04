<template>
  <div class="timer-container">
    <div class="total-days">{{ timeOBJ.days }}天</div>
    <div class="day-block">
      <div class="time-block hours">
        <span class="time-title">时</span>

        <div id="hour-1" class="time-item">
          <span class="top">0</span>
          <span class="top-back">0</span>
          <span class="bottom">0</span>
          <span class="bottom-back">0</span>
        </div>

        <div id="hour-2" class="time-item">
          <span class="top">0</span>
          <span class="top-back">0</span>
          <span class="bottom">0</span>
          <span class="bottom-back">0</span>
        </div>
      </div>
      <div class="time-block minutes">
        <div class="time-block hours">
          <span class="time-title">分</span>

          <div id="minute-1" class="time-item">
            <span class="top">0</span>
            <span class="top-back">0</span>
            <span class="bottom">0</span>
            <span class="bottom-back">0</span>
          </div>

          <div id="minute-2" class="time-item">
            <span class="top">0</span>
            <span class="top-back">0</span>
            <span class="bottom">0</span>
            <span class="bottom-back">0</span>
          </div>
        </div>
      </div>
      <div class="time-block seconds">
        <span class="time-title">秒</span>

        <div id="second-1" class="time-item">
          <span class="top">0</span>
          <span class="top-back">0</span>
          <span class="bottom">0</span>
          <span class="bottom-back">0</span>
        </div>

        <div id="second-2" class="time-item">
          <span class="top">0</span>
          <span class="top-back">0</span>
          <span class="bottom">0</span>
          <span class="bottom-back">0</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { defineComponent, onMounted, onBeforeUnmount, reactive } from "vue";

const modes = ["seq", "dec"];

export default defineComponent({
  props: {
    // 模式选择 正计时 倒计时
    mode: {
      validator(value) {
        return modes.includes(value);
      },
      default: "dec",
    },
    // 从某个时间开始到现在的时长开始计时 正计时时生效
    startTime: String,
    // 时长对象 xxD(天)xxH(小时)xxM(分钟)xxS(秒) 倒计时时生效
    time: String,
  },
  setup(props) {
    const timeOBJ = reactive({
      days: 0,
      hours: 0,
      minutes: 0,
      seconds: 0,
      timeSeconds: 0,
      timeInterval: null,
    });

    // 解析时间为 天 时 分 秒
    const resolveTime = (time) => {
      var timeTmpe = time;
      if (isNaN(timeTmpe) && props.mode === "dec") {
        // const days = time.indexOf("D") ? "" : 0;
      }

      if (isNaN(timeTmpe) && props.mode === "seq") {
        timeTmpe = new Date() - new Date(timeTmpe);
      }
      // 毫秒转为时间
      const days = Math.floor(timeTmpe / 86400000);
      const hours = Math.floor((timeTmpe % 86400000) / 3600000);
      const minutes = Math.floor(((timeTmpe % 86400000) % 3600000) / 60000);
      const seconds = Math.floor(
        (((timeTmpe % 86400000) % 3600000) % 60000) / 1000
      );
      const totalSeconds = timeTmpe / 1000;
      return {
        days,
        hours,
        minutes,
        seconds,
        totalSeconds,
      };
      // 解析字符串对象为number类型的毫秒
    };

    const timeInit = () => {
      if (!(props.startTime || props.time)) return;

      const hour1 = document.getElementById("hour-1");
      const hour2 = document.getElementById("hour-2");
      const min1 = document.getElementById("minute-1");
      const min2 = document.getElementById("minute-2");
      const sec1 = document.getElementById("second-1");
      const sec2 = document.getElementById("second-2");

      const { days, hours, minutes, seconds, totalSeconds } = resolveTime(
        props.mode === "seq" ? props.startTime : props.time
      );
      timeOBJ.timeSeconds = totalSeconds;
      timeOBJ.days = days;
      timeOBJ.hours = hours;
      timeOBJ.minutes = minutes;
      timeOBJ.seconds = seconds;
      if (props.mode === "dec") {
        timeOBJ.timeInterval = setInterval(() => {
          if (timeOBJ.timeSeconds > 0) {
            --timeOBJ.seconds;
            if (timeOBJ.minutes >= 0 && timeOBJ.seconds < 0) {
              timeOBJ.seconds = 59;
              --timeOBJ.minutes;
            }

            if (timeOBJ.hours >= 0 && timeOBJ.minutes < 0) {
              timeOBJ.minutes = 59;
              --timeOBJ.hours;
            }

            if (timeOBJ.days >= 0 && timeOBJ.hours < 0) {
              timeOBJ.hours = 23;
              --timeOBJ.days;
            }

            // 改变页面的值 并赋予动画
            setTime(timeOBJ.hours, hour1, hour2);

            setTime(timeOBJ.minutes, min1, min2);

            setTime(timeOBJ.seconds, sec1, sec2);

            --timeOBJ.timeSeconds;
            // console.log(timeOBJ.timeSeconds);
          } else {
            clearInterval(timeOBJ.timeInterval);
          }
        }, 1000);
      } else if (props.mode === "seq") {
        timeOBJ.timeInterval = setInterval(() => {
          ++timeOBJ.seconds;

          if (timeOBJ.seconds === 60) {
            timeOBJ.seconds = 0;
            ++timeOBJ.minutes;
          }

          if (timeOBJ.minutes === 60) {
            timeOBJ.minutes = 0;
            ++timeOBJ.hours;
          }

          if (timeOBJ.hours === 24) {
            timeOBJ.hours = 0;
            ++timeOBJ.days;
          }

          // 改变页面的值 并赋予动画
          setTime(timeOBJ.hours, hour1, hour2);

          setTime(timeOBJ.minutes, min1, min2);

          setTime(timeOBJ.seconds, sec1, sec2);

          ++timeOBJ.timeSeconds;
          // console.log(timeOBJ.timeSeconds);
        }, 1000);
      }
    };

    const setTime = (value, dom1, dom2) => {
      const val_1 = value.toString().charAt(0);
      const val_2 = value.toString().charAt(1);
      const val_flg_1 = dom1.getElementsByClassName("top")[0].innerHTML;
      const val_flg_2 = dom2.getElementsByClassName("top")[0].innerHTML;
      if (value >= 10) {
        if (val_1 !== val_flg_1) setAnime(val_1, dom1);
        if (val_2 !== val_flg_2) setAnime(val_2, dom2);
      } else {
        if ("0" !== val_flg_1) setAnime(0, dom1);
        if (val_1 !== val_flg_2) setAnime(val_1, dom2);
      }
    };

    const setAnime = (value, dom) => {
      const $top = dom.getElementsByClassName("top")[0];
      const $top_back = dom.getElementsByClassName("top-back")[0];
      const $bottom = dom.getElementsByClassName("bottom")[0];
      const $bottom_back = dom.getElementsByClassName("bottom-back")[0];
      $top_back.innerHTML = value;
      $bottom_back.innerHTML = value;

      $top.setAttribute(
        "style",
        "transition: 0.8s all ease-in-out;transform: rotateX(-180deg)"
      );
      $top_back.setAttribute(
        "style",
        "transition: 0.8s all ease-in-out;transform: rotateX(0deg)"
      );

      setTimeout(() => {
        $top.innerHTML = value;
        $bottom.innerHTML = value;
        $top.setAttribute("style", "transition: none;transform: rotateX(0deg)");
        $top_back.setAttribute(
          "style",
          "transition: none;transform: rotateX(180deg)"
        );
      }, 800);
    };

    onMounted(() => {
      if (modes.includes(props.mode)) {
        timeInit();
      } else {
        console.warn("组件mode参数携带错误");
      }
    });

    onBeforeUnmount(() => {
      clearInterval(timeOBJ.timeInterval);
    });

    return {
      timeOBJ,
    };
  },
});
</script>

<style lang="scss" scoped>
.timer-container {
  .total-days {
    font-size: 65px;
    text-align: center;
    margin-bottom: 20px;
  }

  .time-block {
    float: left;
    margin: 0px 15px;
  }

  .time-title {
    display: block;
    text-align: center;
    font-size: 1.5em;
    margin-bottom: 15px;
    color: #1a1a1a;
  }

  .time-item {
    position: relative;
    float: left;
    height: 110px;
    width: 100px;
    text-align: center;
    margin-right: 10px;
    background-color: #fff;
    border-radius: 8px;
    box-shadow: 0 3px 4px 0 rgb(0 0 0 / 20%),
      inset 2px 4px 0 0 rgb(255 255 255 / 8%);

    &:last-child {
      margin-right: 0;
    }

    & > span {
      position: absolute;
      left: 0;
      right: 0;
      font: normal 6em/110px "Lato";
      margin: auto;
      font-weight: 700;
      color: #de4848;
    }

    .top,
    .bottom-back {
      background-color: #fff;
      &:after {
        content: "";
        position: absolute;
        z-index: 0;
        left: 0;
        bottom: 0;
        width: 100%;
        height: 100%;
        border-top-left-radius: 8px;
        border-top-right-radius: 8px;
        border-bottom: 1px solid rgba(0, 0, 0, 0.1);
      }
    }

    .top,
    .top-back {
      background-color: #fff;
      height: 50%;
      overflow: hidden;
      backface-visibility: hidden;
    }

    .bottom-back {
      height: 50%;
      border-top-left-radius: 8px;
      border-top-right-radius: 8px;
      background-color: #f7f7f7;
      overflow: hidden;

      span {
        position: absolute;
        top: 0;
        left: 0;
        right: 0;
        margin: auto;
      }
    }

    .top {
      z-index: 3;
      background-color: #fff;
      transition: 0.8s all ease-in-out;
      perspective: 300px;
      transform-origin: 50% 100%;
    }

    .top-back {
      z-index: 4;
      background-color: #fff;
      border-radius: 8px;
      position: absolute;
      top: 50%;
      left: 0;
      right: 0;
      line-height: 0px;
      margin: auto;
      transition: 0.8s all ease-in-out;
      perspective: 200px;
      transform-origin: 50% 0%;
      transform: rotateX(180deg);
    }
  }
}
</style>
