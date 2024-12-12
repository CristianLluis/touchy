<template>
  <div class="gradient-bg">
    <div ref="touchArea" class="gradients-container">
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
import { onMounted, ref, reactive, computed } from 'vue'
import Hammer from 'hammerjs'
import { v4 as uuidv4 } from 'uuid'

const clientId = uuidv4()
const touchArea = ref(null)
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
    const size = 200;
    return {
      transform: `translate3d(${this.x - size / 2}px, ${this.y - size / 2}px, 0)`,
      width: `${size}px`,
      height: `${size}px`,
    }
  }
}

function hammertime(event){
  const { center: { x, y } } = event
  const id = `${clientId}-${event.srcEvent.pointerId}`
  console.log(event)
  if (event.isFinal) {
    bubbles.delete(id)
  }
  else if (bubbles.has(id)) {
    bubbles.get(id).update(x, y)
  }
  else {
    bubbles.set(id, new Bubble(id, x, y))
  }
}

onMounted(() => {
  if (touchArea.value) {
    const hammerManager = new Hammer.Manager(touchArea.value);

    hammerManager.add( new Hammer.Pan({ direction: Hammer.DIRECTION_ALL, threshold: 0 }) );
    hammerManager.on("pan", hammertime);
  }

  document.addEventListener('contextmenu', function(e) {
    e.preventDefault();
  });
});

const bubblesArray = computed(() => Array.from(bubbles.values()))
</script>

