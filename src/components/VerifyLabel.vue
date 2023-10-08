<template>
  <body>
    <img id="imageRefId" ref="imageRef" width="1200" height="800" class="detection__canvas">
    <canvas id="rectCanvas" ref="rectCanvasRef" width="1200" height="800" class="canvas"></canvas>
  </body>
</template>

<script lang="ts">
import { defineComponent, ref, reactive, onMounted, toRefs } from 'vue'

export default defineComponent({
  name: "Video",
  props: {
    video: {
      type: Object,
      default: null
    }
  },

  setup(props) {
    console.log("loading...");
    const rectCanvasRef = ref(null);
    const imageRef = ref(null);
    const state = reactive({
      fream_idx: 0,
      canvasCtx: null,
      rects: [[[50, 50, 200, 100, false], [0, 0, 12, 100, false]], [[50, 123, 200, 100, false], [0, 23, 12, 534, false]]]
      // rects: [[50, 50, 1000, 100]]
    });
    const methods = {
      init() {
        state.canvasCtx = rectCanvasRef.value.getContext("2d");
        state.canvasCtx.canvas.addEventListener("mousedown", methods.onMouseDown, false);
        imageRef.value.addEventListener("load", () => {
          state.canvasCtx.clearRect(0, 0, state.canvasCtx.canvas.width, state.canvasCtx.canvas.height);
          var rects = state.rects[state.fream_idx];
          rects.forEach((rect, i) => {
            // state.canvasCtx.restore();
            state.canvasCtx.beginPath();
            state.canvasCtx.rect(rect[0], rect[1], rect[2], rect[3]);
            state.canvasCtx.lineWidth = 7;
            state.canvasCtx.strokeStyle = 'yellow';
            state.canvasCtx.stroke()
          });
          console.log(state.fream_idx, state.rects.length, rects, imageRef.value.src);
        });

        methods.tick();
      },
      sleep(ms) {
        return new Promise(resolve => setTimeout(resolve, ms));
      },
      async tick() {
        imageRef.value.src = "/src/assets/test" + state.fream_idx + ".png"
        state.fream_idx += 1;
        state.fream_idx %= state.rects.length;

        // setTimeout(() => {console.log("1")}, 5000)
        await methods.sleep(10000);
        requestAnimationFrame(() => {
          methods.tick();
        });
      },
      onMouseDown(e) {
        e.stopPropagation();
        var cursor = {
          x: e.offsetX || e.originalEvent.layerX,
          y: e.offsetY || e.originalEvent.layerY
        };

        var rects = state.rects[state.fream_idx];
        var idx = -1;
        rects.forEach((rect, i) => {
          if (cursor.x > rect[0] && cursor.x < rect[0] + rect[2] &&
            cursor.y > rect[1] && cursor.y < rect[1] + rect[3]) {
            idx = i;
            return true;
          }
        });

        if (idx >= 0) {
          var rect = state.rects[state.fream_idx][idx];
          state.canvasCtx.clearRect(rect[0], rect[1], rect[2], rect[3]);
          state.canvasCtx.beginPath();
          state.canvasCtx.rect(rect[0], rect[1], rect[2], rect[3]);
          state.canvasCtx.lineWidth = 7;
          rect[4] = !rect[4];
          state.canvasCtx.strokeStyle = (rect[4] == true ? 'red' : 'yellow');
          state.canvasCtx.stroke()
        }
      }
    };


    onMounted(() => {
      methods.init();
    })

    return {
      rectCanvasRef,
      imageRef,
      ...toRefs(state),
      ...methods,
    };
  }
});
</script>

<style lang="scss" scoped>
.canvas {
  background: rgba(255, 255, 255, 0);
  /*关键点*/
  position: absolute;
  z-index: 1;
  /*确保在遮盖的元素的上方*/
  top: 0px;
  left: 0px;
  border: 5px solid #2e24e1;
}

.detection__canvas {
  position: absolute;
  top: 0;
  left: 0;
}
</style>