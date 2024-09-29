<template>
  <div class="slider-container">
    <div class="slider-background" :style="{ backgroundImage: `url(${backgroundImage})` }">
      <div class="puzzle-piece" :style="{ left: sliderPosition + 'px' }" @mousedown="startDrag"></div>
      <div class="mask" :style="{ left: targetPosition + 'px' }"></div>
    </div>
    <button @click="reset">重置</button>
    <p v-if="message">{{ message }}</p>
  </div>
</template>

<script>
import { ref } from 'vue';

export default {
  setup() {
    const sliderPosition = ref(0);
    const targetPosition = ref(300);
    const isDragging = ref(false);
    const message = ref('');
    const backgroundImage = ref('path/to/your/image.jpg');

    const startDrag = (e) => {
      isDragging.value = true;
      document.addEventListener('mousemove', onDrag);
      document.addEventListener('mouseup', stopDrag);
    };

    const onDrag = (e) => {
      if (isDragging.value) {
        const backgroundRect = document.querySelector('.slider-background').getBoundingClientRect();
        let newPosition = e.clientX - backgroundRect.left - 25;
        newPosition = Math.max(0, Math.min(newPosition, backgroundRect.width - 50));
        sliderPosition.value = newPosition;
      }
    };

    const stopDrag = () => {
      isDragging.value = false;
      document.removeEventListener('mousemove', onDrag);
      document.removeEventListener('mouseup', stopDrag);

      if (Math.abs(sliderPosition.value - targetPosition.value) < 10) {
        message.value = '验证成功!';
      } else {
        message.value = '验证失败，请再试一次。';
      }
    };

    const reset = () => {
      sliderPosition.value = 0;
      message.value = '';
      targetPosition.value = Math.floor(Math.random() * (300 - 50));
    };

    return {
      sliderPosition,
      targetPosition,
      startDrag,
      reset,
      message,
      backgroundImage,
    };
  },
};
</script>

<style>
.slider-container {
  width: 400px;
  margin: auto;
}

.slider-background {
  position: relative;
  height: 200px;
  background-color: #f0f0f0;
  overflow: hidden;
  background-size: cover;
}

.puzzle-piece {
  position: absolute;
  width: 50px;
  height: 50px;
  background-color: #007bff; /* 可以换成拼图的图片 */
  clip-path: polygon(0 0, 100% 0, 100% 100%, 0 100%, 50% 50%); /* 示例的拼图形状 */
  cursor: pointer;
  transition: left 0.1s;
}

.mask {
  position: absolute;
  width: 50px;
  height: 50px; /* 设置遮罩的高度 */
  background-color: #ff0000; /* 可以换成拼图的图片 */
  clip-path: polygon(0 0, 100% 0, 100% 100%, 0 100%, 50% 50%); /* 示例的拼图形状 */
  pointer-events: none;
}
</style>
