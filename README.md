# vue-dragzoomable

> 一个约 5KB 的 Vue 3 单文件组件：按住拖拽平移，滚轮缩放，**缩放以光标为锚点**——光标下的那个点始终不动。

## 用法

没有构建、没有 npm 包，就是把 `index.vue` 拷进你的项目：

```vue
<template>
  <DragZoomable ref="view" style="width: 100%; height: 600px">
    <img src="./big.png" alt="" />
  </DragZoomable>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import DragZoomable from './index.vue'

const view = ref<InstanceType<typeof DragZoomable>>()

// 需要时手动操作
// view.value?.reset(0, 0, 1)   // 归位到指定位置与缩放
// view.value?.center()         // 内容居中
// view.value?.resize()         // 按容器重新适配并居中
</script>
```

`defineExpose` 暴露三个方法：`reset(x, y, scale)`、`center()`、`resize()`。

## 行为

| 能力 | 实现 |
| --- | --- |
| 平移 | `mousedown / mousemove / mouseup / mouseleave`；位移按 `clientX/Y` 增量累加到 `position`，落在容器的 `translate` 上 |
| 缩放 | `wheel` 事件，`scale += -deltaY * 0.01`，限制在 **0.3 ~ 4** |
| 锚点缩放 | 先按比例改 `scale`，再用改之前光标在内容里的相对比例（`wrate / hrate`）反算新的 `translate`，使光标下的点保持不动 |
| 滚轮锁 | `isWheelLocked` + `nextTick`，避免一次滚动在连续 wheel 事件里被重复叠加 |
| 自适应 | `resize()` 比较容器与内容尺寸，内容更大时取 `min` 比例、更小时取 `max` 比例，再居中 |
| 调试标记 | `mark(x, y, color)` 在容器上临时打点（10 秒后自动移除），用于验证锚点是否真的不动 |

## 注意

- 样式用了 `<style lang="scss" scoped>`，项目里需要有 sass。
- 当前版本 `zoom()` 用到 `nextTick` 但 import 里漏了它，使用前请补上：`import { ref, onMounted, onBeforeUnmount, nextTick } from 'vue'`。

## License

MIT
