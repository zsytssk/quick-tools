<template>
  <div :class="$style.page">
    <div class="btn-list">
      <el-button @click="copy" :disabled="!textarea">复制</el-button>
      <el-button @click="convertString" :disabled="!textarea">转换</el-button>
    </div>
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

const convertString = () => {
  const isJsonStr = textarea.value.indexOf('\\n') !== -1
  console.log(`test:>`, isJsonStr)
  if (!isJsonStr) {
    textarea.value = JSON.stringify(textarea.value)
  } else {
    textarea.value = JSON.parse(textarea.value)
  }
}

const copy = () => {
  navigator.clipboard
    .writeText(textarea.value)
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
    .btn-list {
      display: flex;
      margin-top: 10px;
      padding: 10px;
      & > .el-button {
        flex: 1;
      }
    }
    .el-textarea {
      flex: 1;
      .el-textarea__inner {
        height: 100%;
      }
    }
  }
}
</style>
