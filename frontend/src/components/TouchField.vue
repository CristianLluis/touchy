<template>
  <div class="gradient-bg">
    <div
      class="gradients-container"
      @touchstart.prevent="onTouchstart"
      @touchmove="onTouchmove"
      @touchend="onTouchend"
      @touchcancel="onTouchcancel"
    >
      <!-- <div class="testBubble bubble"></div> -->
      <div
        v-for="bubble in bubblesArray"
        :key="bubble.id"
        :style="bubble.getStyle()"
        class="bubble"
      ></div>
      <svg>
        <filter id="liquid">
          <feGaussianBlur in="SourceGraphic" stdDeviation="20" />
          <feColorMatrix
            values="
          1 0 0 0 0
          0 1 0 0 0
          0 0 1 0 0
          0 0 1 20 -10
          "
          ></feColorMatrix>
        </filter>
      </svg>
    </div>
  </div>
</template>

<script setup>
import { reactive, computed } from 'vue'
import { v4 as uuidv4 } from 'uuid'

const clientId = uuidv4()
const bubbles = reactive(new Map())

class Bubble {
  constructor(id, x, y) {
    this.id = id
    this.x = x
    this.y = y
  }
  update(x, y) {
    this.x = x
    this.y = y
  }
  getStyle() {
    const size = 100
    const offsetX = -30 // Adjust this value to move horizontally (negative moves left)
    const offsetY = -30 // Adjust this value to move vertically (negative moves up)
    return {
      transform: `translate3d(${this.x - size / 2 + offsetX}px, ${this.y - size / 2 + offsetY}px, 0)`,
      width: `${size}px`,
      height: `${size}px`,
    }
  }
}

function getTouchData(touch) {
  const id = `${clientId}-${touch.identifier}`
  const x = touch.pageX
  const y = touch.pageY
  return { id, x, y }
}

function onTouchstart(event) {
  Array.from(event.changedTouches).forEach((touch) => {
    const { id, x, y } = getTouchData(touch)
    bubbles.set(id, new Bubble(id, x, y))
  })
}

function onTouchmove(event) {
  Array.from(event.changedTouches).forEach((touch) => {
    const { id, x, y } = getTouchData(touch)
    bubbles.get(id).update(x, y)
  })
}

function onTouchend(event) {
  Array.from(event.changedTouches).forEach((touch) => {
    const { id } = getTouchData(touch)
    bubbles.delete(id)
  })
}

function onTouchcancel() {
  bubbles.clear()
}

const bubblesArray = computed(() => Array.from(bubbles.values()))
</script>

<style>
.testBubble {
  height: 100px;
  width: 100px;
  position: relative;
  background: linear-gradient(45deg, #ffeb3b, #da00ff);
  border-radius: 50%;
  opacity: 1;
  top: 100px;
  left: 100px;
}
</style>
