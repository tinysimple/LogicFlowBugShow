<template>
  <div class="hello">
    <div class="left-panel">
      {{ size }}
      <select v-model="size">
        <option
          v-for="item in sizeOption"
          :key="item.value"
          :value="item.value"
          :label="item.width + ' * ' + item.height"
        />
      </select>
      <div style="margin-top: 40px;font-size: 14px;">
        <span>修改上方的尺寸后画布中的class="lf-graph"元素的width、height发生了变化，但是其上层元素依旧保留初始化时的width、height。<br />导致id为“container”的元素无法居中显示。</span>
      </div>
    </div>
    <div class="center-panel">
      <div
        ref="container"
        class="lf-container"
        id="container"
      ></div>
    </div>
    <div class="right-panel">right</div>
  </div>
</template>

<script setup>
import { ref, onMounted, watch, computed } from "vue";

import LogicFlow from "@logicflow/core";
import "@logicflow/core/dist/index.css";
import "@logicflow/extension/lib/index.css";

let lf = null;

const size = ref("large");
const sizeOption = ref([
  { value: "small", width: 400, height: 600 },
  { value: "medium", width: 800, height: 500 },
  { value: "large", width: 1080, height: 800 },
  { value: "extraLarge", width: 1920, height: 1080 }
]);

const currentSize = computed(() => {
  return sizeOption.value.filter((item) => item.value === size.value)[0];
});

watch(
  () => size.value,
  () => {
    if (!lf) {
      return;
    }
    const { width, height } = currentSize.value || {};
    lf.resize(width, height);
  }
);

onMounted(() => {
  init();
});

const config = {
  isSilentMode: false,
  stopScrollGraph: true,
  stopZoomGraph: true,
  style: {
    rect: {
      rx: 5,
      ry: 5,
      strokeWidth: 2
    },
    circle: {
      fill: "#f5f5f5",
      stroke: "#666"
    },
    ellipse: {
      fill: "#dae8fc",
      stroke: "#6c8ebf"
    },
    polygon: {
      fill: "#d5e8d4",
      stroke: "#82b366"
    },
    diamond: {
      fill: "#ffe6cc",
      stroke: "#d79b00"
    },
    text: {
      color: "#b85450",
      fontSize: 12
    }
  }
};

const data = {
  nodes: [
    {
      id: "1",
      type: "rect",
      x: 150,
      y: 100,
      text: "矩形"
    },
    {
      id: "2",
      type: "circle",
      x: 350,
      y: 100,
      properties: {},
      text: {
        x: 350,
        y: 100,
        value: "line one\nline two\nline three\nline four"
      }
    },
    {
      id: "3",
      type: "ellipse",
      x: 550,
      y: 100,
      text: "椭圆"
    },
    {
      id: "4",
      type: "polygon",
      x: 150,
      y: 250,
      text: "多边形"
    },
    {
      id: "5",
      type: "diamond",
      x: 350,
      y: 250,
      text: "菱形"
    },
    {
      id: "6",
      type: "text",
      x: 550,
      y: 250,
      text: "纯文本节点"
    },
    {
      id: "7",
      type: "html",
      x: 150,
      y: 400,
      text: "html节点"
    }
  ]
};

function init() {
  let width, height;
  if (currentSize.value) {
    width = currentSize.value.width;
    height = currentSize.value.height;
  }
  lf = new LogicFlow({
    ...config,
    container: document.querySelector("#container"),
    width,
    height,
    grid: {
      size: 10
    }
  });
  lf.render(data);
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.hello {
  display: flex;
  height: 840px;
  width: 100%;
  overflow: hidden;
}
.left-panel,
.right-panel {
  flex-basis: 200px;
  flex-shrink: 0;
  background: antiquewhite;
}
.center-panel {
  flex: 1;
  display: flex;
  justify-content: center;
  align-items: center;
  background: #efefef;
  overflow: auto;
  font-size: 0;
}
.lf-container {
  overflow: auto;
  max-width: 100%;
  max-height: 100%;
}
</style>
