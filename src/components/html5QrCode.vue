<script setup lang="ts">
import { ElMessage } from 'element-plus'
import type { CameraDevice } from 'html5-qrcode'
import { onMounted, ref, watch } from 'vue'

import {
  getCameraId,
  getCameras,
  scanCode,
  scanFile,
  stopScan,
} from '../utils/html5-qrcode'

defineProps<{}>()
defineOptions({ name: 'HTML5-Qrcode' })

const selectCamera = ref<string>()
const cameraList = ref<CameraDevice[]>([])
const resultText = ref('')
const inputFile = ref<HTMLInputElement>()
const startScanCamera = async () => {
  if (!selectCamera.value) {
    ElMessage.error('请选择摄像头')
    return
  }
  await stopScan()
  const [err2, str2] = await scanCode(
    '1',
    selectCamera.value,
    'reader',
    (text) => {
      resultText.value = text
    },
  )
  if (err2) {
    console.error(str2)
    return
  }
}

const uploadFile = (inputEle?: HTMLInputElement) => {
  return new Promise<[boolean, string | File]>((resolve) => {
    if (!inputEle) {
      return resolve([true, 'cant find input ele for upload!'] as const)
    }
    const fn = (event: any) => {
      const files = event.target?.files

      if (files.length > 0) {
        inputEle.removeEventListener('change', fn)
        return resolve([false, files[0]] as const)
      } else {
        console.log('没有文件被选择')
      }
    }

    inputEle.addEventListener('change', fn)
    inputEle?.click()
  })
}

const startScanFile = async () => {
  await stopScan()

  const [err, file] = await uploadFile(inputFile.value)
  if (err) {
    console.error(file)
    return
  }

  const [err2, str2] = await scanFile('1', 'reader', file as File)

  if (err2) {
    console.error(str2)
    return
  }
  resultText.value = str2
}

watch(
  () => selectCamera.value,
  () => {
    startScanCamera()
  },
)
const stop = async () => {
  await stopScan()
  resultText.value = ''
}

onMounted(() => {
  stopScan()
  getCameras().then(([err, list]) => {
    if (err) {
      return
    }
    cameraList.value = list as CameraDevice[]
  })
  getCameraId().then(([err, cameraId]) => {
    if (err) {
      return
    }
    selectCamera.value = cameraId
  })
})
</script>

<template>
  <div class="box">
    <div class="inner">
      <div id="reader"></div>
      <div class="allText">{{ resultText ? resultText : '~' }}</div>
      <div style="opacity: 0">
        <input
          ref="inputFile"
          type="file"
          accept="image/*"
          style="pointer-events: none" />
      </div>
      <div>
        <el-select v-model="selectCamera">
          <el-option
            v-for="(item, index) in cameraList"
            :value="item.id"
            :label="item.label"
            :key="index" />
        </el-select>
      </div>
      <div class="card">
        <button
          type="button"
          @click="startScanCamera">
          startScanCamera
        </button>
        <button
          type="button"
          @click="startScanFile">
          startScanFile
        </button>
        <button
          type="button"
          @click="stop">
          stop
        </button>
      </div>
    </div>
  </div>
</template>

<style scoped lang="scss">
.box {
  display: flex;
  align-items: center;
  margin: 0;
  background-color: #242424;
  min-height: 100vh;
  .inner {
    margin: 0 auto;
    width: 300px;
    .allText {
      width: 100%;
      overflow: hidden;
      color: #fff;
      text-align: center;
      word-break: break-all;
    }
    #reader {
      margin: 0 auto;
      background: #fff;
      width: 300px;
      height: 300px;
      overflow: hidden;
    }
    .card {
      display: flex;
      justify-content: center;
      gap: 10px;
    }
  }
}
</style>
