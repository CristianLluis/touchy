<template>
  <div class="gradient-bg">
    <div
      class="gradients-container"
      @touchstart.prevent="onTouchstart"
      @touchmove="onTouchmove"
      @touchend="onTouchend"
      @touchcancel="onTouchcancel"
    >
      <div
        v-for="bubble in bubblesArray"
        :key="bubble.id"
        :style="bubble.getStyle()"
        class="bubble"
      ></div>
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
    const size = 200
    return {
      transform: `translate3d(${this.x - size / 2}px, ${this.y - size / 2}px, 0)`,
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
