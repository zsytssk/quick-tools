<template>
  <div :class="$style.page">
    <el-button @click="copy" :disabled="!textarea">复制</el-button>
    <el-input
      v-model="textarea"
      :rows="2"
      resize="none"
      type="textarea"
      placeholder="Please input"
    />
  </div>
</template>

<script setup lang="ts">
import { ElMessage } from 'element-plus'
import { ref } from 'vue'

const textarea = ref<string>('')

const copy = () => {
  navigator.clipboard
    .writeText(JSON.stringify(textarea.value))
    .then(() => {
      ElMessage.success('已复制到剪贴板')
    })
    .catch((err) => {
      ElMessage.error('复制失败:', err)
    })
}
</script>

<style lang="scss" module>
.page {
  display: flex;
  flex-direction: column;
  gap: 10px;
  height: 100vh;
  :global {
    .el-textarea {
      flex: 1;
      .el-textarea__inner {
        height: 100%;
      }
    }
  }
}
</style>
