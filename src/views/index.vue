<template>
  <div class="index-container">
    <timer />
  </div>
</template>

<script>
import { defineComponent, reactive, toRefs } from "vue";
import Timer from "@/components/timer.vue";
export default defineComponent({
  components: {
    Timer,
  },
  setup() {
    const state = reactive({
      temp: "",
    });
    return {
      ...toRefs(state),
    };
  },
});
</script>

<style lang="scss" scoped>
.index-container {
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: aliceblue;
}
</style>
