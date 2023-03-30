---
title: Vue3单独封装Table
description: Vue3+TypeScript+ElementPlus单独封装Table
tags:
  - vue3
  - table
cover: 'https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/202303301416217.png'
top_img: 'https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/202303301416217.png'
abbrlink: 43495
date: 2023-03-30 14:11:31
---

# 单独封装Table

> 使用Vue3 + Element Plus单独封装Table

### 封装

```vue
<template>
  <el-table
    :data="tableData"
    style="width: 100%"
    @selection-change="handleSelectionChange"
  >
    <el-table-column v-if="index" type="index" width="50" />
    <el-table-column v-if="checkbox" type="selection" width="55" />
    <template v-for="item in column" :key="item">
      <!-- 判断文本类 -->
      <el-table-column
        v-if="item.type === 'function'"
        :prop="item.prop"
        :label="item.label"
      >
        <template #default="scope">
          <div v-html="item.callback && item.callback(scope.row)" />
        </template>
      </el-table-column>
      <!-- 判断是否为Solt -->
      <el-table-column
        v-else-if="item.type === 'solt'"
        :prop="item.prop"
        :label="item.label"
      >
        <template #default="scope">
          <slot :name="item.slot_name" :data="scope.row" />
        </template>
      </el-table-column>
      <!-- 普通的 -->
      <el-table-column
        v-else
        :key="item.prop"
        :prop="item.prop"
        :label="item.label"
        :width="item.width"
      />
    </template>
  </el-table>
</template>

<script setup>
import { onMounted, defineEmits } from "vue";

defineOptions({
  name: "MyTable"
});
const emit = defineEmits(["onload", "updateList"]);

const props = defineProps({
  tableData: {
    type: Array,
    required: true,
    default: () => []
  },
  column: {
    type: Array,
    required: true,
    default: () => []
  },
  checkList: {
    type: Array,
    default: () => []
  },
  checkbox: Boolean,
  index: Boolean,
  onLoad: Boolean
});
onMounted(() => {
  api();
});
const handleSelectionChange = value => {
  emit("updateList", value);
};
const api = () => {
  // 请求数据成功后抛出
  props.onLoad && emit("onload", "后台数据");
};
</script>
```



### 如何使用

```vue
<template>
  <div>
    <MyTable :column = column checkbox on-load @onload='onload' @updateList='updateList'>
      <template v-slot:operation="slot">
        <el-button type="primary" @click="jump(slot.data)">编辑</el-button>
        <el-button type="primary">删除</el-button>
      </template>
    </MyTable>
  </div>
</template>

<script setup>
import MyTable from '../components/table/index.vue'
import { reactive, watch, ref } from 'vue'
let checkList = []
const onload = (data) => {
  console.log(data);
}
const jump = (data) => {
  console.log(data);
}
const updateList = (data) => {
  checkList = data
}
const column = [
  { label:'日期', prop:'date' },
  { label:'姓名', prop:'name' },
  { type:'function',
    label:'地址',
    prop:'address',
    callback:(data) => {
      return `<a href='#'>${data.address}</a>`
    }
  },
  {
    type:'solt',
    label:'操作',
    prop:'operation',
    slot_name:'operation',
  }
]
const tableData = ref([])
</script>
<style scoped>
</style>
```



> TODO：后续会再进行二次开发