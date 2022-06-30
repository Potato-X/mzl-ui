<template>
  <div class="mui-slider" ref="slider">
    <div class="mui-slider-rail"></div>
    <div class="mui-slider-track" :style="{width:`calc(${offsetX} + 10px)`}"></div>
    <div
      :data-msg="currentValue"
      class="mui-slider-handle"
      @mousedown="mousedownHandler"
      :style="{ left: offsetX }"
    ></div>
  </div>
</template>
<script>
export default {
  name: "mSlider",
  watch: {
    handleState(newValue) {
      console.log(newValue);
    },
  },
  mounted() {
    document.body.addEventListener("mouseup", this.mouseupHandler);
  },
};
</script>
<script setup>
import { ref, computed, onMounted } from "vue";
const props = defineProps({
  modelValue: {
    type: Number,
    default: 0,
  },
  min:{
    type: Number,
    default: 0,
  },
  max:{
    type: Number,
    default: 100,
  },
  range:{
    type: Boolean,
    default: false,
  }
});
const emit = defineEmits(["update:modelValue"])
const handleState = ref(false);
// const offsetX = ref("");
const slider = ref("slider");
// const currentValue = ref(0);
let sliderOffsetX = "";
let handleOffsetX = "";
let sliderWidth = 0;
let total = 0;
let track = 0;
function mousedownHandler(event) {
  handleState.value = true;
  handleOffsetX = event.offsetX;
  document.body.addEventListener("mousemove", mousemoveHandler);
}
function mouseupHandler(event) {
  handleState.value = false;
  document.body.style.userSelect = "text";
  document.body.removeEventListener("mousemove", mousemoveHandler);
}
const offsetX = computed({
  get(){
    return `calc(${(props.modelValue/props.max)*100}% - 10px)`
  },
  set(){
    
  }
})
const currentValue = computed({
  get(){
    return props.modelValue
  },
  set(value){
    emit("update:modelValue", value);
  }
})
//拖动
function mousemoveHandler(event) {
  if (handleState.value) {
    document.body.style.userSelect = "none";
    track = 0;
    track = event.clientX - handleOffsetX - sliderOffsetX;
    if (track >= 0) {
      if (track >= sliderWidth - 10) {
        track = `${sliderWidth - 10}`;
      } else {
        track = `${event.clientX - handleOffsetX - sliderOffsetX-10}`;
      }
    } else {
      track = 0;
    }
    offsetX.value = `${(track/total) * 100}%`;
    currentValue.value = Math.floor((track / total) * 100);
  }
}
onMounted(() => {
  sliderOffsetX = slider.value.getBoundingClientRect().left;
  sliderWidth = slider.value.clientWidth;
  total = sliderWidth - 16;
});
</script>

<style scoped lang="scss">
.mui-slider {
  height: 12px;
  position: relative;
  margin: 10px 6px;
  padding: 4px 0;
  cursor: pointer;
  box-sizing: border-box;
  &-rail {
    position: absolute;
    width: 100%;
    background-color: #f5f5f5;
    border-radius: 2px;
    height: 4px;
  }
  &-track {
    position: relative;
    background-color: rgb(24, 144, 255);
    border-radius: 2px;
    height: 4px;
    z-index: 1;
  }
  &-handle {
    position: absolute;
    height: 16px;
    width: 16px;
    border-radius: 50%;
    border: solid 3px rgb(145, 213, 255);
    background-color: #ffffff;
    left: 0;
    transform: translateX(-50%);
    top: 50%;
    transform: translateY(-50%);
    cursor: pointer;
    z-index: 2;
    transition: border-color 0.3s, box-shadow 0.6s,
      transform 0.3s cubic-bezier(0.18, 0.89, 0.32, 1.28);
    &::after {
      position: absolute;
      display: block;
      content: attr(data-msg);
      bottom: calc(100% + 10px);
      color: #ffffff;
      left: 50%;
      transform: translateX(-50%);
      padding: 10px;
      height: 14px;
      border-radius: 2px;
      font-size: 18px;
      line-height: 14px;
      text-align: center;
      background-color: rgba($color: #000000, $alpha: 0.8);
      opacity: 0;
      transition:  all linear .08s;
      
    }
  }
  &:hover &-rail {
    background-color: #e1e1e1;
  }
  &-handle:hover {
    border-color: rgb(24, 144, 255);
  }
  &-handle:hover::after {
    opacity: 1;
  }
}
</style>