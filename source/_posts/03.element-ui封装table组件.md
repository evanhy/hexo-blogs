---
title: element-ui封装table组件
description: 封装简单的table组件
tags:
  - element-ui
categories:
  - 后台系统
abbrlink: 24658
date: 2021-10-01 21:48:11
cover:
---

> myTable 组件
```html
<template>
    <div class="table-container">
        <el-table
            :data="tableData"
            width="100%"
            :row-class-name="rowClassName"
            :height="height"
            :row-style="{height: `${rowHeight}px`}">
            <template v-for="(item, index) in tableTitle">
                <slot v-if="item.slot" :name="item.slot"></slot>
                <el-table-column
                    v-else
                    :key="index"
                    :prop="item.property"
                    :label="item.label"
                    :min-width="item.minWidth ? item.minWidth : ''"
                    :width="item.width ? item.width : ''">
                </el-table-column>
            </template>
        </el-table>
    </div>
</template>

<script>
export default {
    name: 'myTable',
    props: {
        tableData: { // 表格数据
            type: Array,
            required: true,
            default: () => {
                return []
            }
        },
        tableTitle: { // 表格头标题
            type: Array,
            require: true
        },
        height: { // 表格高度
            type: [Number, String],
            default: '100%'
        },
        rowHeight: { // 表格行高
            type: [Number, String],
            default: 44
        }
    },
    data() {
        return {}
    },
    methods: {
        // 可以通过指定 Table 组件的 row-class-name 属性来为 Table 中的某一行添加 class，表明该行处于某种状态。
        rowClassName(e) {
            return e.rowIndex % 2 === 0 ? '' : 'light-line'
        }
    }
}
</script>

<!-- 样式 可以自定义 -->
<style scoped lang="scss">
.table-container {
    /deep/ .el-table {
        background-color: transparent;

        &::before { // 表格底部边框
            background: none;
        }

        tbody tr:hover > td { // 表格触碰样式
            background-color: #F5F7FA;
        }
    }

    /deep/ .el-table__header-wrapper {
        .el-table__cell { // 表头样式
            height: 44px;
            padding: 0;
            background: #FFFFFF;
            border-bottom: #EBEEF5 solid 1px !important;
            text-align: center;
        }
    }

    /deep/ .el-table__body-wrapper {
        &::-webkit-scrollbar { // 表格滚动条
            width: 0 !important;
        }

        .el-table__row { // 表格行样式
            background-color: #F5F7FA;

            .el-table__cell {
                padding: 0;
                text-align: center;
                border-bottom: #EBEEF5 solid 1px !important;
            }
        }

        .light-line { // 高亮行颜色
            background-color: #FFFFFF;
        }
    }
}
</style>
```

> 使用： father组件
```html
<template>
    <div class="father">
        <my-table :table-title="tableTitle"
                  :table-data="tableData"></my-table>
        <!-- <my-table
                        :table-title="tableTitle"
                        :table-data="tableData">
                    <el-table-column slot="handle" label="操作">
                        <template slot-scope="scope">
                            <el-button @click="handleClick(scope.row)">查看</el-button>
                        </template>
                    </el-table-column>
                </my-table> -->
    </div>
</template>

<script>
import MyTable from "./myTable";

export default {
    name: "father",
    components: {MyTable},
    data() {
        return {
            tableTitle: [
                {
                    label: '日期',
                    property: 'date'
                },
                {
                    label: '姓名',
                    property: 'name'
                },
                {
                    label: '地址',
                    property: 'address'
                },
                {
                    slot: 'handle'
                }
            ],
            tableData: [
                {
                    date: '2016-05-02',
                    name: '王小虎',
                    address: '上海市普陀区金沙江路 1518 弄'
                },
                {
                    date: '2016-05-04',
                    name: '王小虎',
                    address: '上海市普陀区金沙江路 1517 弄'
                },
                {
                    date: '2016-05-01',
                    name: '王小虎',
                    address: '上海市普陀区金沙江路 1519 弄'
                },
                {
                    date: '2016-05-03',
                    name: '王小虎',
                    address: '上海市普陀区金沙江路 1516 弄'
                }
            ]
        }
    },
    created() {
    },
    methods: {}
}
</script>

<style lang="less" scoped>

</style>
```
