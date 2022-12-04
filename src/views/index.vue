<template>
  <div class="love-index-container">
    <div class="back-wrapper">
      <img src="@/assets/picture/ikon-bike.svg" />
    </div>
    <div class="container-header">
      <span>我 们 已 经 相 恋</span>
    </div>
    <timer mode="seq" start-time="2021-03-14" />
    <div class="container-footer">
      <div class="footer-inner">
        <div class="city-box">
          <img src="@/assets/icon/chongqing.svg" />
        </div>
        <div class="progress">
          <span class="title">距离我们见面还有 {{ days }} 天</span>
          <div class="progress-inner">
            <div
              class="progress-bar"
              :style="{
                width: ((60 - days) / 60) * 100 + '%',
              }"
            ></div>
          </div>
        </div>
        <div class="city-box">
          <img src="@/assets/icon/shenzhen.svg" />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { defineComponent, onMounted, reactive, toRefs } from "vue";
import Timer from "@/components/timer.vue";
export default defineComponent({
  components: {
    Timer,
  },
  setup() {
    const meetDay = "2023-01-22";
    const state = reactive({
      days: 0,
    });

    onMounted(() => {
      // 计算剩余天数
      state.days = Math.floor((new Date(meetDay) - new Date()) / 86400000);
    });
    return {
      ...toRefs(state),
    };
  },
});
</script>

<style lang="scss" scoped>
.love-index-container {
  position: relative;
  height: 100vh;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  background-color: aliceblue;

  .back-wrapper {
    position: absolute;
    z-index: 0;
  }

  .container {
    &-header {
      // position: absolute;
      margin-bottom: 20px;
      span {
        font-size: 45px;
      }
    }

    &-footer {
      position: absolute;
      bottom: 0px;
      .footer-inner {
        padding: 10px;
        display: flex;
        background: #fff;
        border-top-left-radius: 45px;
        border-top-right-radius: 45px;
        border-top: 2px solid #ff9e9e;
      }

      .progress {
        display: flex;
        flex-direction: column;
        justify-content: center;
        .title {
          // text-align: center;
          margin-bottom: 5px;
        }
        &-inner {
          height: 15px;
          width: 300px;
          border-radius: 45px;
          background-color: #e5e4e6;
        }
        &-bar {
          height: 15px;
          border-radius: 45px;
          width: 100%;
          background-color: #ff9e9e;
        }
      }

      .city-box {
        height: 50px;
        width: 50px;

        img {
          height: 100%;
        }
      }
    }
  }
}
</style>
