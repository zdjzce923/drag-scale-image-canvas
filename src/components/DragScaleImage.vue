<script setup>
import * as spritejs from 'spritejs';
import panzoom from 'panzoom'
import { onMounted, ref } from 'vue'
import PercentControl from './PercentControl.vue'

const percent = ref(0)
const natureWidth = ref(0)
const natureHeight = ref(0)
let instance = null
onMounted(() => {
  const { Scene, Sprite, Label } = spritejs;
  const container = document.getElementById('adaptive');
  const scene = new Scene({
    container,
    width: 1000,
    height: 1000,
    // contextType: '2d',
  });
  const layer = scene.layer();

  (async function () {
    await scene.preload({
      id: 'robot',
      src: 'https://images.unsplash.com/photo-1566378246598-5b11a0d486cc?ixlib=rb-4.0.3&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=687&q=80',
    });

    const robot = new Sprite('robot');
    robot.attr({
      pos: [0, 0],
      scale: 1,
    });
    layer.append(robot);

  }());

  instance = panzoom(container)
  const image = new Image()
  image.src = 'https://images.unsplash.com/photo-1566378246598-5b11a0d486cc?ixlib=rb-4.0.3&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=687&q=80'
  image.onload = () => {
    natureWidth.value = image.width
    natureHeight.value = image.height
    const decimal = (500 / natureWidth.value).toFixed(2)
    instance.zoomTo(0, 0, decimal)
    percent.value = decimal * 100
  }

  instance.on('zoom', (e) => {
    const trans = instance.getTransform()
    // console.log('trans', trans)
    percent.value = Math.floor(trans.scale.toFixed(2) * 100)
  })


})

const updatePercent = (e) => {
  let scale = (e / percent.value).toFixed(2)
  console.log('e-----', e)
  console.log('percent.value', percent.value)
  percent.value = e
  // const decimal = percent.value / 100
  instance.smoothZoom(0, 0, scale )
}
</script>

<template>
  <div class="control-image-contain">
    <!-- eslint-disable-next-line -->
    <PercentControl :percent="percent" @update:percent="updatePercent" />
    <div class="adaptive-container">
      <div id="adaptive"></div>
    </div>
  </div>
</template>

<style scoped>
.control-image-contain {
  padding: 30px;
  border-radius: 10px;
  background: #2C3333;

}

.adaptive-container {
  margin-top: 30px;
  border-radius: 10px;
  padding: 10px;
  background: #5b7279;
  max-width: 500px;
  max-height: 1000px;
  overflow: hidden;
}

#adaptive {
  width: 1200px;
  height: 600px;
}

.read-the-docs {
  color: #888;
}
</style>
