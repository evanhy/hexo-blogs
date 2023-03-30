---
title: vue3封装echarts（升级版）
tags:
  - vue3
  - echarts
cover: 'https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/202303301133906.png'
top_img: 'https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/202303301136939.png'
abbrlink: 9983
date: 2023-03-30 11:34:14
---



> 项目中经常用到echarts，不做封装直接拿来使用也行，但不可避免要写很多重复的配置代码，封装稍不注意又会过度封装，丢失了扩展性和可读性。始终没有找到一个好的实践，偶然看到一篇文章，给了灵感。找到了一个目前认为用起来很舒服的封装。

# 思路

1. 结合项目需求，针对不同类型的图表，配置基础的默认通用配置，例如x/y，label，图例等的样式
2. 创建图表组件实例（不要使用`id`,容易重复，还需要操作`dom`，直接用`ref`获取当前组件的`el`来创建图表），提供`type`(图表类型)，和`options`(图表配置)两个必要属性
3. 根据传入`type`，加载默认的图表配置
4. 深度监听传入的`options`，变化时更新覆盖默认配置，更新图表
5. **提供事件支持，支持`echart`事件按需绑定交互**

> 注意要确保所有传入图表组件的options数组都是[shallowReactive](https://cn.vuejs.org/api/reactivity-advanced.html#shallowreactive)类型，避免数组量过大，深度响应式导致性能问题

## 目录结构



```go
├─v-charts
│  │  index.ts     // 导出类型定义以及图表组件方便使用
│  │  type.d.ts    // 各种图表的类型定义
│  │  useCharts.ts // 图表hooks
│  │  v-charts.vue // echarts图表组件
│  │
│  └─options // 图表配置文件
│          bar.ts
│          gauge.ts
│          pie.ts

```

![目录结构](https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/202303301114540.png)

------

# 组件代码

## v-charts.vue



```typescript
<template>
  <div class="v-charts" ref="chartRef" style="width: 100%; height: 100%" />
</template>
<script lang="ts" setup>
import { onMounted, PropType, shallowRef, toRefs, watch } from "vue";
import * as echarts from "echarts/core";
import { ChartsEvents, ChartType, useCharts } from "./useCharts";

/**
 * echarts事件类型
 * 截至目前，vue3类型声明参数必须是以下内容之一，暂不支持外部引入类型参数
 * 1. 类型字面量
 * 2. 在同一文件中的接口或类型字面量的引用
 * // 文档中有说明：https://cn.vuejs.org/api/sfc-script-setup.html#typescript-only-features
 */
interface EventEmitsType {
  <T extends ChartsEvents.EventType>(
    e: `${T}`,
    event: ChartsEvents.Events[Uncapitalize<T>]
  ): void;
}

defineOptions({
  name: "VCharts"
});

// 定义props，提供ts支持
const props = defineProps({
  // 定义Echarts类型
  type: {
    type: String as PropType<ChartType>,
    default: "bar"
  },
  // 定义Echarts配置项
  options: {
    type: Object as PropType<echarts.EChartsCoreOption>,
    default: () => ({})
  }
});

// 定义事件，提供ts支持
defineEmits<EventEmitsType>();

// https://cn.vuejs.org/api/reactivity-utilities.html#toref
const { type, options } = toRefs(props);
// https://cn.vuejs.org/api/reactivity-advanced.html#shallowref
const chartRef = shallowRef();

const { charts, setOptions, initChart } = useCharts({ type, el: chartRef });

onMounted(async () => {
  // 初始化图表
  await initChart();
  // 设置图表配置项
  setOptions(options.value);
});
// 监听options变化，更新图表
watch(
  options,
  () => {
    setOptions(options.value);
  },
  {
    deep: true
  }
);

// 可以通过 defineExpose 编译器宏来显式指定在 <script setup> 组件中要暴露出去的属性
defineExpose({
  $charts: charts
});
</script>
<style lang="scss" scoped>
.v-charts {
  width: 100%;
  height: 100%;
  min-height: 200px;
}
</style>
```

## useCharts.ts



```js
// 图表hooks
import { ChartType } from "./type";
import * as echarts from "echarts/core";
import {
  ShallowRef,
  Ref,
  onBeforeUnmount,
  shallowRef,
  shallowReactive,
  useAttrs
} from "vue";

import {
  TitleComponent,
  LegendComponent,
  TooltipComponent,
  GridComponent,
  DatasetComponent,
  TransformComponent
} from "echarts/components";

// 引入图表类型
import { BarChart, LineChart, PieChart, GaugeChart } from "echarts/charts";

// 引入图表渲染器
import { LabelLayout, UniversalTransition } from "echarts/features";
// 引入canvas渲染器
import { CanvasRenderer } from "echarts/renderers";

const optionsModules = import.meta.glob<{ default: echarts.EChartsCoreOption }>(
  "./options/**.ts"
);

interface ChartHookOption {
  type?: Ref<ChartType>;
  el: ShallowRef<HTMLElement>;
}

/**
 *  视口变化时echart图表自适应调整
 */
class ChartsResize {
  #charts = new Set<echarts.ECharts>(); // 缓存已经创建的图表实例
  #timeId = null;

  constructor() {
    window.addEventListener("resize", this.handleResize.bind(this)); // 视口变化时调整图表
  }

  getCharts() {
    return [...this.#charts];
  }

  handleResize() {
    clearTimeout(this.#timeId);
    this.#timeId = setTimeout(() => {
      this.#charts.forEach(chart => {
        chart.resize();
      });
    }, 500);
  }

  add(chart: echarts.ECharts) {
    this.#charts.add(chart);
  }

  remove(chart: echarts.ECharts) {
    this.#charts.delete(chart);
  }

  removeListener() {
    window.removeEventListener("resize", this.handleResize);
  }
}

// 创建一个实例
export const chartsResize = new ChartsResize();

// 图表hooks
export const useCharts = ({ type, el }: ChartHookOption) => {
  echarts.use([
    BarChart,
    LineChart,
    BarChart,
    PieChart,
    GaugeChart,
    TitleComponent,
    LegendComponent,
    TooltipComponent,
    GridComponent,
    DatasetComponent,
    TransformComponent,
    LabelLayout,
    UniversalTransition,
    CanvasRenderer
  ]);
  // 初始化完成标识
  let initComplete = false;
  // 图表实例
  const charts = shallowRef<echarts.ECharts>();

  // 图表配置
  let options!: echarts.EChartsCoreOption;

  // 获取图表配置
  const getOptions = async () => {
    const moduleKey = `./options/${type.value}.ts`;
    const { default: defaultOption } = await optionsModules[moduleKey]();
    return defaultOption;
  };

  // 设置图表配置
  const setOptions = (opt: echarts.EChartsCoreOption) => {
    initComplete && charts.value.setOption(opt);
  };

  // 初始化图表
  const initChart = async () => {
    charts.value = echarts.init(el.value);
    options = await getOptions();
    charts.value.setOption(options);
    chartsResize.add(charts.value); // 将图表实例添加到缓存中
    initEvent(); // 添加事件支持
    initComplete = true;
  };

  /**
   * 初始化事件
   */
  const attrs = useAttrs();
  const initEvent = () => {
    Object.keys(attrs).forEach(attrKey => {
      if (/^on/.test(attrKey)) {
        const cb = attrs[attrKey];
        attrKey = attrKey.replace(/^on(Chart)?/, "");
        attrKey = `${attrKey[0]}${attrKey.substring(1)}`;
        typeof cb === "function" && charts.value?.on(attrKey, cb as () => void);
      }
    });
  };

  onBeforeUnmount(() => {
    chartsResize.remove(charts.value); // 移除缓存
  });

  return {
    charts,
    setOptions,
    initChart,
    initEvent
  };
};

export const chartsOptions = <T extends echarts.EChartsCoreOption>(option: T) =>
  shallowReactive<T>(option);

export * from "./type.d";
```

## type.d.ts



```ts
/*
 * @Description:
 * @Version: 2.0
 * @Autor: GC
 * @Date: 2022-03-02 10:21:33
 * @LastEditors: GC
 * @LastEditTime: 2022-06-02 17:45:48
 */
// import * as echarts from 'echarts/core';
import * as echarts from 'echarts'
import { XAXisComponentOption, YAXisComponentOption } from 'echarts';

import { ECElementEvent, SelectChangedPayload, HighlightPayload,  } from 'echarts/types/src/util/types'

import {
  TitleComponentOption,
  TooltipComponentOption,
  GridComponentOption,
  DatasetComponentOption,
  AriaComponentOption,
  AxisPointerComponentOption,
  LegendComponentOption,
} from 'echarts/components';// 组件
import {
  // 系列类型的定义后缀都为 SeriesOption
  BarSeriesOption,
  LineSeriesOption,
  PieSeriesOption,
  FunnelSeriesOption,
  GaugeSeriesOption
} from 'echarts/charts';

type Options = LineECOption | BarECOption | PieECOption | FunnelOption

type BaseOptionType = XAXisComponentOption | YAXisComponentOption | TitleComponentOption | TooltipComponentOption | LegendComponentOption | GridComponentOption

type BaseOption = echarts.ComposeOption<BaseOptionType>

type LineECOption = echarts.ComposeOption<LineSeriesOption | BaseOptionType>

type BarECOption = echarts.ComposeOption<BarSeriesOption | BaseOptionType>

type PieECOption = echarts.ComposeOption<PieSeriesOption | BaseOptionType>

type FunnelOption = echarts.ComposeOption<FunnelSeriesOption | BaseOptionType>

type GaugeECOption = echarts.ComposeOption<GaugeSeriesOption | GridComponentOption>

type EChartsOption = echarts.EChartsOption;

type ChartType = 'bar' | 'line' | 'pie' | 'gauge'

// echarts事件
namespace ChartsEvents {
  // 鼠标事件类型
  type MouseEventType = 'click' | 'dblclick' | 'mousedown' | 'mousemove' | 'mouseup' | 'mouseover' | 'mouseout' | 'globalout' | 'contextmenu' // 鼠标事件类型
  type MouseEvents = {
    [key in Exclude<MouseEventType,'globalout'|'contextmenu'> as `chart${Capitalize<key>}`] :ECElementEvent
  }
  // 其他的事件类型极参数
  interface Events extends MouseEvents {
    globalout:ECElementEvent,
    contextmenu:ECElementEvent,
    selectchanged: SelectChangedPayload;
    highlight: HighlightPayload;
    legendselected: { // 图例选中后的事件
      type: 'legendselected',
      // 选中的图例名称
      name: string
      // 所有图例的选中状态表
      selected: {
        [name: string]: boolean
      }
    };
    // ... 其他类型的事件在这里定义
  }


  // echarts所有的事件类型
  type EventType = keyof Events
}

export {
  BaseOption,
  ChartType,
  LineECOption,
  BarECOption,
  Options,
  PieECOption,
  FunnelOption,
  GaugeECOption,
  EChartsOption,
  ChartsEvents
}


```

## options/bar.ts



```ts
import { BarECOption } from "../type";
const options: BarECOption = {
  legend: {},
  tooltip: {},
  xAxis: {
    type: "category",
    axisLine: {
      lineStyle: {
        // type: "dashed",
        color: "#C8D0D7"
      }
    },
    axisTick: {
      show: false
    },
    axisLabel: {
      color: "#7D8292"
    }
  },
  yAxis: {
    type: "value",
    alignTicks: true,
    splitLine: {
      show: true,
      lineStyle: {
        color: "#C8D0D7",
        type: "dashed"
      }
    },
    axisLine: {
      lineStyle: {
        color: "#7D8292"
      }
    }
  },
  grid: {
    left: 60,
    bottom: "8%",
    top: "20%"
  },
  series: [
    {
      type: "bar",
      barWidth: 20,
      itemStyle: {
        color: {
          type: "linear",
          x: 0,
          x2: 0,
          y: 0,
          y2: 1,
          colorStops: [
            {
              offset: 0,
              color: "#62A5FF" // 0% 处的颜色
            },
            {
              offset: 1,
              color: "#3365FF" // 100% 处的颜色
            }
          ]
        }
      }
      // label: {
      //   show: true,
      //   position: "top"
      // }
    }
  ]
};
export default options;

```

# 项目中使用

## index.vue



```html
<template>
  <div class="home">
    <section class="bottom">
      <div class="device-statistics chart-box">
        <div class="title">累计设备接入统计</div>
        <v-charts
          type="bar"
          :options="statisDeviceByUserObjectOpts"
          @selectchanged="selectchanged"
          @chart-click="handleChartClick"
        />
      </div>
      <div class="coordinate-statistics chart-box">
        <div class="title">坐标数据接入统计</div>
        <v-charts type="bar" :options="statisCoordAccess" />
      </div>
    </section>
  </div>
</template>
<script lang="ts" setup>
import {
  useStatisDeviceByUserObject,
} from "./hooks";
// 设备分类统计
const { options: statisDeviceByUserObjectOpts,selectchanged,handleChartClick } = useStatisDeviceByUserObject();
</script>

```

## /hooks/useStatisDeviceByUserObject.ts



```ts
export const useStatisDeviceByUserObject = () => {
  // 使用chartsOptions确保所有传入v-charts组件的options数据都是## shallowReactive浅层作用形式，避免大量数据导致性能问题
  const options = chartsOptions<BarECOption>({
    yAxis: {},
    xAxis: {},
    series: []
  });
  const init = async () => {
    const xData = [];
    const sData = [];
    const dicts = useHashMapDics<["dev_user_object"]>(["dev_user_object"]);
    const data = await statisDeviceByUserObject();
    dicts.dictionaryMap.dev_user_object.forEach(({ label, value }) => {
      if (value === "6") return; // 排除其他
      xData.push(label);
      const temp = data.find(({ name }) => name === value);
      sData.push(temp?.qty || 0);
      
      // 给options赋值时要注意options是浅层响应式
      options.xAxis = { data: xData }; 
      options.series = [{ ...options.series[0], data: sData }];
    });
  };
  
  // --------- 事件 S ----------
  const selectchanged = (params: ChartsEvents.Events["selectchanged"]) => {
    console.log(params, "选中图例了");
  };

  const handleChartClick = (params: ChartsEvents.Events["chartClick"]) => {
    console.log(params, "点击了图表");
  };
  // --------- 事件 E ----------
  onMounted(() => {
    init();
  });
  return {
    options，
    selectchanged,
    handleChartClick
  };
};

```

> 使用时输入@可以看到组件支持的所有事件：

![image-20230330112948554](https://huyu-blog.oss-cn-hangzhou.aliyuncs.com/img/image-20230330112948554.png)



- [完整demo](https://gitee.com/guochao0822/v-chart-charts-demo)
- 
