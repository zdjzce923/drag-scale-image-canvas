<script setup>
import * as spritejs from 'spritejs';
import panzoom from 'panzoom'
import { onMounted, ref } from 'vue'
import PercentControl from './PercentControl.vue'

const percent = ref(0)
const natureWidth = ref(0)
const natureHeight = ref(0)
const drawSprite = ref(null)
let instance = null
onMounted(() => {
  // 将容器的宽高设为图片的宽高 绘制时以容器宽高为标准绘画 再进行等比缩放
  const { Scene, Sprite, Label, Rect } = spritejs;
  const container = document.getElementById('adaptive');
  const image = new Image()
  image.src = 'https://images.unsplash.com/photo-1566378246598-5b11a0d486cc?ixlib=rb-4.0.3&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=687&q=80'
  image.onload = () => {
    natureWidth.value = image.width
    natureHeight.value = image.height
    container.style.width = image.width + 'px'
    container.style.height = image.height + 'px'

    const scene = new Scene({
      container,
      mode: 'stickyWidth',
      width: image.width,
      height: image.height
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

      drawSprite.value = new Sprite({
        normalize: true,
        // pos: [300, 500],
        pos: [300, 500],
        size: [200, 50],
        bgcolor: 'rgba(54,202,177,0.16)',
        borderWidth: 1,
        borderDash: 3,
        borderDashOffset: 20,
        borderRadius: 10,
        borderColor: '#36CAB1'
      })

      layer.append(drawSprite.value)

    }());


    instance = panzoom(container, {
      maxZoom: 2,
      minZoom: 0,
    })

    const decimal = (500 / natureWidth.value).toFixed(2)
    console.log('decimal', decimal)
    instance.zoomTo(0, 0, decimal)
    percent.value = decimal * 100

    instance.on('zoom', (e) => {
      const trans = instance.getTransform()
      percent.value = Math.floor(trans.scale.toFixed(2) * 100)
    })

    instance.on('pan', (e) => {
      // console.log('e======', e)
    })
  }


})



const updatePercent = (e) => {
  let scale = (e / percent.value).toFixed(2)
  percent.value = e
  instance.smoothZoom(0, 0, scale)
}

const drawLabelChange = (e) => {
  const x = Math.random() * natureWidth.value
  const y = Math.random() * natureHeight.value
  console.log(x)
  console.log(y)
  drawSprite.value.attr({
    pos: [x, y],
    size: [200, 50],
  })
}
</script>

<template>
  <div class="control-image-contain">
    <div class="change"  @click="drawLabelChange">click here change draw label position</div>
    <!-- eslint-disable-next-line -->
    <PercentControl :percent="percent" @update:percent="updatePercent" />
    <div class="adaptive-container">
      <div id="adaptive"></div>
    </div>
  </div>
</template>

<style scoped>
.change {
  border-radius: 8px;
  border: 1px solid transparent;
  padding: 0.6em 1.2em;
  font-size: 1em;
  font-weight: 500;
  font-family: inherit;
  background-color: #1a1a1a;
  cursor: pointer;
  transition: border-color 0.25s;
  margin: 0 auto;
  margin-bottom: 20px;
  background-color: #f9f9f9;
  max-width: 70%;
}

.control-image-contain {
  padding: 30px;
  border-radius: 10px;
  background: #2C3333;

}

.adaptive-container {
  margin-top: 30px;
  border-radius: 10px;
  padding: 0px;
  background: #5b7279;
  max-width: 500px;
  max-height: 600px;
  overflow: hidden;
}

#adaptive {
  width: 600px;
  height: 1000px;
}

.read-the-docs {
  color: #888;
}
</style>
