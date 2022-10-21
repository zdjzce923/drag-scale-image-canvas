<template>
  <div>
    <span @click="btnClick('add')">+</span>
    <span>percent: {{percent}} %</span>
    <span @click="btnClick('sub')">-</span>
  </div>
</template>

<script setup>
import { onBeforeMount, onMounted, getCurrentInstance, defineComponent, ref, defineEmits } from 'vue'
const props = defineProps({
  percent: Number
})
const emit = defineEmits(['update:percent'])
const btnClick = (type) => {
  let percentVal = props.percent
  if (type === 'sub') {
    percentVal - 25 < 0 ? percentVal = 0 : percentVal -= 25
  } else {
    percentVal + 25 > 200 ? percentVal = 200 : percentVal += 25
  }

  emit('update:percent', percentVal)

}
</script>
<style scoped>
div {
  border-radius: 20px;
  margin: 0 auto;
  max-width: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 10px;
  background: #E7F6F2;
}

span {
  color: #395B64;
  padding: 5px 15px;
  font-size: 30px;
  cursor: pointer;
  border-radius: 10px;
  transition: all 0.3s;
}

span:nth-child(2) {
  padding: 0 14px;
  font-size: 15px;
  cursor: auto;
}

span:nth-child(1):hover,
span:nth-child(3):hover {
  color: white;
  background: #A5C9CA;
}
</style>