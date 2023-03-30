---
title: Vue3封装Table
description: Vue3 + Element Plus 二次封装Table
tags:
  - vue3
  - element
cover: 'https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/00539.webp'
top_img: 'https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/00539.webp'
abbrlink: 37031
date: 2023-03-30 14:08:43
---

{% referfrom '[1]','单独封装Table','https://www.huyu001.top/posts/43495.html' %}
{% referfrom '[2]','单独封装分页','https://www.huyu001.top/posts/42451.html' %}


# Vue3封装Table

> 在我们前端开发的过程中常见的需求有列表加分页加查询，尤其在后台管理系统中，在这里我封装了一套基于vue3 + element-plus + ts的组件，直接在组件中使用封装的组件和编写可配置文件即可,直接上代码，开箱即可用



#### 先从简单的开始，分页组件 Pagination

- 在components下新建文件夹Pagination,里面再建index.vue，代码如下

```vue
<script lang="ts" setup>
import { computed } from "vue";

defineOptions({
  name: "MyPagination"
});

const props = defineProps({
  // total 总条数
  total: {
    required: true,
    type: Number,
    default: 0
  },
  // page 当前页
  page: {
    type: Number,
    default: 1
  },
  //  每页条数
  limit: {
    type: Number,
    default: 20
  },
  // 每页条数选项
  pageSizes: {
    type: Array,
    default() {
      return [10, 20, 50, 100];
    }
  },
  // 分页布局
  layout: {
    type: String,
    default: "total,  prev, pager, next, sizes, jumper"
  },
  // 是否显示背景色
  background: {
    type: Boolean,
    default: true
  },
  // 是否自动滚动到顶部
  autoScroll: {
    type: Boolean,
    default: true
  }
});
// 定义emit
const emit = defineEmits(["pagination", "update:page", "update:limit"]);

// 定义当前页
const currentPage = computed({
  get() {
    return props.page;
  },
  set(newValue) {
    // 更新当前页
    emit("update:page", newValue);
    console.log(newValue);
  }
});
// 定义每页条数
const pageSize = computed({
  get() {
    return props.limit;
  },
  set(newValue) {
    // 更新每页条数
    emit("update:limit", newValue);
  }
});

// 监听每页条数变化
const handleSizeChange = val => {
  emit("pagination", { page: currentPage.value, limit: val });
};
// 监听当前页变化
const handleCurrentChange = val => {
  emit("pagination", { page: val, limit: pageSize.value });
};
</script>

<template>
  <div class="CardPagination">
    <el-pagination
      v-model:current-page="currentPage"
      v-model:page-size="pageSize"
      :page-sizes="pageSizes"
      :layout="layout"
      :total="total"
      :background="background"
      v-bind="$attrs"
      @current-change="handleCurrentChange"
      @size-change="handleSizeChange"
    />
  </div>
</template>

<style lang="scss" scoped>
.CardPagination {
  display: flex;
  justify-content: center;
}
</style>

```



#### 其次是查询form组件 Form， 里面一些小的样式可自己修改，这里只是我的样式

- 在components下新建文件夹Form,里面再建index.vue，代码如下

```vue
<template>
  <div>
    <el-row :gutter="18" style="display: block !important">
      <el-form
        ref="form"
        class="form_flex"
        :inline="true"
        :label-width="`${labelWidth}px`"
      >
        <el-col v-for="(item, index) of items" :key="index" v-bind="colLayout">
          <el-form-item :label="item.name">
            <template v-if="item.slot">
              <slot :name="item.slotName" />
            </template>
            <el-input
              v-else-if="!item.type || item.type === 'input'"
              :model-value="searchParams[item.key]"
              v-bind="setAttrs(item)"
              :placeholder="item.placeholder || '请输入'"
              @update:model-value="handleValueChange($event, item.key)"
            />
            <el-input
              v-else-if="item.type === 'textarea'"
              :model-value="searchParams[item.key]"
              v-bind="setAttrs(item)"
              :placeholder="item.placeholder || '请输入'"
              @update:model-value="handleValueChange($event, item.key)"
            />
            <el-select
              v-else-if="item.type === 'select'"
              v-bind="setAttrs(item)"
              :model-value="searchParams[item.key]"
              :placeholder="item.placeholder || '请选择'"
              @update:model-value="handleValueChange($event, item.key)"
            >
              <el-option
                v-for="(option, i) of item.options"
                :key="i"
                :label="
                  option[(item['props'] && item['props']['key']) || 'label']
                "
                :value="
                  option[(item['props'] && item['props']['value']) || 'value']
                "
              />
            </el-select>
            <el-cascader
              v-else-if="item.type === 'cascader'"
              :model-value="searchParams[item.key]"
              v-bind="setAttrs(item)"
              :options="item.options"
              :placeholder="item.placeholder || '请选择'"
              :props="item.props"
              @update:model-value="handleValueChange($event, item.key)"
            />
            <el-date-picker
              v-else-if="item.type === 'date-picker'"
              v-bind="setAttrs(item)"
              :default-time="defaultTime"
              end-placeholder="结束日期"
              :model-value="searchParams[item.key]"
              :placeholder="item.placeholder || '请选择'"
              prefix-icon=""
              range-separator="-"
              start-placeholder="开始日期"
              :type="item.pickerType || 'daterange'"
              @update:model-value="handleValueChange($event, item.key)"
            />
            <template v-else-if="item.type === 'section'">
              <el-input
                class="section-input"
                :model-value="searchParams[item.minKey]"
                v-bind="setAttrs(item)"
                :placeholder="item.placeholder || '请输入'"
              />
              <span class="symbol">-</span>
              <el-input
                class="section-input"
                :model-value="searchParams[item.maxKey]"
                v-bind="setAttrs(item)"
                :placeholder="item.placeholder || '请输入'"
              />
            </template>
          </el-form-item>
        </el-col>
        <el-col :span="btnWidth">
          <slot name="prefix-button" />
          <el-button-group class="search-wrap">
            <el-button type="primary" @click="handleSearch">
              <vab-icon icon="file-search-fill" />
              查询
            </el-button>
            <el-button @click="handleReset">
              <vab-icon icon="refresh-line" />
              重置
            </el-button>
          </el-button-group>
          <slot name="suffix-button" />
        </el-col>
      </el-form>
    </el-row>
  </div>
</template>
<script>
  export default defineComponent({
    name: 'MyForm',
    props: {
      labelWidth: {
        type: Number,
        default: 80,
      },
      // 表单配置
      items: {
        type: Array,
        default: () => {
          return []
        },
      },
      // 表单参数
      searchParams: {
        type: Object,
        default: () => {
          return {}
        },
      },
      btnWidth: {
        type: Number,
        default: 4,
      },
      colLayout: {
        type: Object,
        default: () => ({
          xl: 5,
          lg: 5,
          md: 12,
          sm: 24,
          xs: 24,
        }),
      },
    },
    emits: ['update:searchParams', 'change', 'search', 'reset'],
    setup(props, { emit }) {
      const form = reactive({
        initSearchParams: {},
      })
      const defaultTime = ref([
        new Date(2000, 1, 1, 0, 0, 0),
        new Date(2000, 2, 1, 23, 59, 59),
      ])

      onMounted(() => {
        // 保存初始值
        form.initSearchParams = _.cloneDeep(props.searchParams)
      })
      const handleValueChange = (value, field) => {
        emit('update:searchParams', { ...props.searchParams, [field]: value })
      }

      // 查询
      const handleSearch = () => {
        const cpValue = _.cloneDeep(props.searchParams)
        let request = true
        if (!cpValue.requestFlag) request = true
        if (cpValue.requestFlag) request = false
        emit('update:searchParams', { ...cpValue, request })
        emit('change', cpValue)
        emit('search', cpValue)
      }
      // 重置
      const handleReset = () => {
        const cpValue = _.cloneDeep(form.initSearchParams)
        let request = true
        if (!cpValue.requestFlag) request = true
        if (cpValue.requestFlag) request = false
        emit('update:searchParams', { ...cpValue, request })
        emit('change', cpValue)
        emit('reset', cpValue)
      }
      const setAttrs = (params) => {
        const { slot, ...options } = params
        return { ...options, slot }
      }
      return {
        defaultTime,
        handleValueChange,
        setAttrs,
        handleReset,
        handleSearch,
      }
    },
  })
</script>

<style lang="scss" scoped>
  .form_flex {
    display: flex;
    flex-wrap: wrap;
    .search-wrap {
      margin-bottom: 16px;
    }
    :deep() {
      .el-select {
        width: 100%;
      }
      .el-cascader {
        width: 100%;
      }
      .el-form-item {
        display: flex;
        width: 100%;
        margin-bottom: 18px;
        .el-form-item__label {
          font-size: 14px;
          color: #606060d9;
        }
        .el-form-item__content {
          flex: 1;
        }
      }
      .el-date-editor.el-input,
      .el-date-editor.el-input__inner {
        width: 100% !important;
      }
      .el-range-editor.el-input__inner {
        padding: 3px 1px 3px 5px;
      }
    }
  }
</style>
```



#### 最后就是table组件的封装了，需要引入分页组件

- 在components下新建文件夹Table,里面再建index.vue和render.ts，代码如下
- render.ts代码如下

```typescript
export default {
  props: {
    row: Object,
    render: Function,
    index: Number,
    column: {
      type: Object,
      default: null,
    },
  },
  setup: function (props: any, context: any) {
    return () =>
      props.render({
        ...props,
        ...context.attrs,
      })
  },
}
```

- index.vue代码如下

```vue
<template>
  <div>
    <el-table
      v-loading="loading"
      :border="true"
      v-bind="setAttrs(tableAttr)"
      :data="tableData"
      stripe
      @selection-change="selectionChange"
    >
      <!-- :height="state.height" -->
      <el-table-column
        v-if="selectVisible"
        align="left"
        :selectable="selectable"
        type="selection"
        width="45"
      />
      <template v-for="(column, index) in columns" :key="index">
        <el-table-column
          v-if="column.render"
          v-bind="setAttrs(column)"
          show-overflow-tooltip
        >
          <template #default="scope">
            <Render
              :column="column"
              :index="scope.$index"
              :render="column.render"
              :row="scope.row"
              v-bind="$attrs"
            />
          </template>
        </el-table-column>
        <el-table-column
          v-else-if="column.type === 'index'"
          type="index"
          v-bind="setAttrs(column)"
          width="70"
        />
        <el-table-column
          v-else
          :key="column.prop"
          show-overflow-tooltip
          v-bind="setAttrs(column)"
        />
      </template>
    </el-table>
    <!-- 分页 -->
    <Pagination
      v-if="!hidden"
      v-model:limit="pageOpt.pageSize"
      v-model:page="pageOpt.pageNum"
      :page-sizes="pageSizes"
      :total="total"
      @pagination="pagination"
    />
  </div>
</template>

<script>
  import Render from './render'
  import { resetParams } from '@/utils'
  import request from '@/utils/request'
  import Pagination from '@/plugins/Pagination/index.vue'
  export default defineComponent({
    name: 'SrmTable',
    components: { Render, Pagination },
    props: {
      url: {
        type: String,
        default: '',
      },
      // 筛选条件
      params: {
        type: Object,
        default: () => ({}),
      },
      // 分页查询数据
      pageRequest: {
        type: Object,
        default: () => ({
          pageNum: 1,
          pageSize: 20,
        }),
      },
      // 外部传入的table数据源 不走组件内接口
      sourceData: {
        type: Array,
        default: () => [],
      },
      contentType: {
        type: String,
        default: 'application/json',
      },
      isLoad: {
        type: Boolean,
        default: true,
      },
      columns: {
        type: Array,
        default: () => [],
      },
      // 每页条数选择器
      pageSizes: {
        type: Array,
        default: () => [10, 20, 50, 100],
      },
      // 控制分页组件的显示隐藏
      hidden: {
        type: Boolean,
        default: false,
      },
      // table组件的属性设置
      tableAttr: {
        type: Object,
        default: () => ({
          border: true,
        }),
      },
      // 是否可选择行
      selectVisible: {
        type: Boolean,
        default: false,
      },
      selectable: {
        type: Function,
        default: () => {},
      },
    },
    emits: [
      'getTotal',
      'update:params',
      'update:pageRequest',
      'pagination',
      'selectionChange',
    ],

    setup(props, { emit }) {
      const $baseTableHeight = inject('$baseTableHeight')

      const state = reactive({
        height: $baseTableHeight() - 30,
        loading: false,
        downloadLoading: false,
        selections: [], // 当前选择项的集合
        tableData: [],
        total: 0,
        pageOpt: props.pageRequest,
        selfLoading: true,
      })
      // 判断是否有外部数据源传入
      const isExternal = computed(() => {
        const { sourceData = [] } = props
        return sourceData.length
      })

      onMounted(() => {
        if (!isExternal.value && props.isLoad) {
          initTable()
        }
      })
      // c初始化数据
      const initTable = async () => {
        state.loading = true
        const realParams = resetParams(props.params)
        const reqOpt = props.url.split('|')
        const [method, url] = reqOpt[1] ? reqOpt : ['post', reqOpt[0]]
        const reqKey = reqOpt[2]
          ? reqOpt[2]
          : method.toLowerCase() === 'post'
          ? 'data'
          : 'params'
        try {
          const { data } = await request({
            url,
            method,
            [reqKey]: {
              ...state.pageOpt,
              ...realParams,
            },
            headers: {
              'Content-Type': props.contentType,
            },
          })
          setTimeout(() => {
            state.loading = false
            state.tableData = data.records || data.list
            state.total = data.total
            emit('getTotal', state.total)
          }, 500)
        } catch (e) {
          state.loading = false
        }
      }
      // 分页
      const pagination = (ev) => {
        const { page, limit } = ev
        state.pageOpt.pageNum = page
        state.pageOpt.pageSize = limit
        // 有外部数据源注入时不走内部分页
        if (isExternal.value) {
          emit('update:pageRequest', state.pageOpt)
          emit('pagination', ev)
        } else {
          initTable()
        }
      }
      const selectionChange = (selections) => {
        emit('selectionChange', selections)
      }
      // 给元素设置属性
      const setAttrs = (params) => {
        const { ...options } = params
        if (!options.align) {
          options.align = 'left'
        }
        return { ...options }
      }

      watchEffect(() => {
        if (props.sourceData.length) {
          state.tableData = props.sourceData
        }
      })
      watch(
        () => props.params,
        (params) => {
          const { request } = params
          if (request) {
            state.pageOpt.pageNum = 1
            initTable()
          }
          params['request'] = false
          emit('update:params', params)
        }
      )
      return {
        ...toRefs(state),
        isExternal,
        pagination,
        selectionChange,
        setAttrs,
        initTable,
      }
    },
  })
</script>
<style lang="scss">
  .el-popper {
    max-width: 50%;
  }
</style>
```

#### 最后说下怎么使用，举例：

<img src="https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/image-20230330134632530.png" alt="image-20230330134632530" style="zoom:50%;" />

- index.vue组件写法如下：

```vue
<form-cpn v-model:search-params="tableParams" :items="formConfig" />
<srm-table
  ref="tableRef"
  :columns="contentTableConfig"
  :handel-delete="handelDelete"
  :handle-edit="handleEdit"
  :params="tableParams"
  url="post|/user/page"
/>
import contentTableConfig from './config/content.config'
import formConfig from './config/form.config'
```

- content.config.js代码如下：

```javascript
import { ElLink, ElDivider, ElTag } from 'element-plus'
export default [
  {
    label: '编号',
    width: 80,
    type: 'index',
  },
  { label: '员工姓名', prop: 'staffName' },
  {
    label: '角色',
    render(props) {
      const { roleVOList } = props.row
      return [
        roleVOList.map((item) => {
          return <ElTag type="success">{item.name}</ElTag>
        }),
      ]
    },
  },
  { label: '手机号', prop: 'phone' },
  { label: '邮箱', prop: 'email' },
  { label: '创建时间', prop: 'createTime' },
  {
    label: '状态',
    width: 100,
    align: 'center',
    render(props) {
      const { row } = props
      return [
        <ElTag effect="dark" type={['success', 'warning'][row.state]}>
          {['启用', '停用'][row.state]}
        </ElTag>,
      ]
    },
  },
  {
    label: '操作',
    align: 'center',
    render(props) {
      const { row } = props
      return [
        <ElLink type="primary" onClick={() => props['handle-edit'](row)}>
          编辑
        </ElLink>,
        <ElDivider direction="vertical" />,
        <ElLink type="danger" onClick={() => props['handel-delete'](row)}>
          删除
        </ElLink>,
      ]
    },
  },
]
```

- form.config.js代码如下：

```js
export default [
  { name: '姓名', key: 'userName', clearable: true },
  { name: '手机号', key: 'mobile', clearable: true },
  {
    name: '创建时间',
    key: 'picker:beginTime,endTime',
    type: 'date-picker',
    span: 6,
  },
]
```

> 以上就是一套简单的封装，如有不足，可以提出问题，欢迎大家补充
