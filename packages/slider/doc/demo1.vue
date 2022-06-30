<!-- html -->
<template>
  <m-slider
  v-model="currentValue"
  ></m-slider>
  {{currentValue}}
</template>

<!-- js -->
<script setup>
import { reactive, ref } from "vue";
const currentValue = ref(50)
</script>