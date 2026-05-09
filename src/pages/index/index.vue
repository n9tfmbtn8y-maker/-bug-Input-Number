<template>
  <view class="container">
    <wd-input-number
      v-model="value"
      :precision="2"
      :step="0.01"
      :min="2"
      :immediate-change="false"
    />
  </view>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const value = ref(2)
</script>

<style lang="scss" scoped>
.container {
  padding: 20px;
}
</style>
