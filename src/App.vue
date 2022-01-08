<template>
  <div class="main">
    <div class="control">
      <el-button @click="isAddDataDialogShow = true">添加数据</el-button>
      <el-button @click="removeLatestData">删除最新数据</el-button>
    </div>
    <div class="charts">
      <v-chart :option="chartOptions"/>
    </div>
  </div>
  <el-dialog v-model="isAddDataDialogShow" >
    <el-input v-model="addDataDialogData.name">
      <template #prepend>名称</template>
    </el-input>
    <el-input v-model="addDataDialogData.value">
      <template #prepend>值</template>
    </el-input>
    <template #footer>
      <span class="add-data-dialog-footer">
        <el-button @click="onAddDataDialogCancel">取消</el-button>
        <el-button @click="onAddDataDialogConfirm">添加</el-button>
      </span>
    </template>
  </el-dialog>
</template>
<script setup lang="ts">
import { EChartsOption } from 'echarts'
import { GridComponent } from 'echarts/components'
import { use } from 'echarts/core'
import { SVGRenderer } from 'echarts/renderers'
import { LineChart } from 'echarts/charts'
import VChart from 'vue-echarts'
import { computed, ref } from 'vue'

type Data = { name: string, value: number }

use([SVGRenderer, LineChart, GridComponent])

// 添加数据对话框
const isAddDataDialogShow = ref(false)
// 添加数据对话框数据
const addDataDialogData = ref<Data>({ name: '', value: 0 })
// 重置添加数据对话框
const resetAddDataDialog = () => {
  addDataDialogData.value = { name: '', value: 0 }
}
// 确认添加按钮被点击
const onAddDataDialogConfirm = () => {
  data.value.push({ name: addDataDialogData.value.name, value: addDataDialogData.value.value })
  resetAddDataDialog()
  isAddDataDialogShow.value = false
}
// 删除最新数据按钮被点击
const removeLatestData = () => {
  data.value.pop()
}
// 取消添加按钮被点击
const onAddDataDialogCancel = () => {
  resetAddDataDialog()
  isAddDataDialogShow.value = false
}


// 图表数据
const data = ref<Data[]>([])
// x 轴 坐标数据
const namesData = computed<string[]>(() => {
  const names: string[] = []
  data.value.forEach(it => names.push(it.name))
  return names
})
// y 轴数据
const valuesData = computed<number[]>(() => {
  const values: number[] = []
  data.value.forEach(it => values.push(it.value))
  return values
})
// 转换成图表能用的数据
const chartOptions = computed<EChartsOption>(() => ({
  xAxis: {
    type: 'category',
    data: namesData.value
  },
  yAxis: {
    type: 'value'
  },
  series: [
    {
      type: 'line',
      data: valuesData.value
    }
  ]
}))
</script>
<style lang="less">
html, body {
  margin: 0;
}
html, body, #app, .main {
  height: 100%;
}
.main {
  display: flex;
  flex-direction: column;
  .charts {
    flex: 1;
  }
}
</style>
