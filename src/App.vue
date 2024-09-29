<template>
  <div class="slider-container">
    <div class="slider-background" :style="{ backgroundImage: `url(${backgroundImage})` }">
      <div class="slider" :style="{ left: sliderPosition + 'px' }" @mousedown="startDrag"></div>
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
    const targetPosition = ref(300); // 目标位置，实际应用中可以随机生成
    const isDragging = ref(false);
    const message = ref('');
    const backgroundImage = ref('path/to/your/image.jpg'); // 你的背景图路径

    const startDrag = (e) => {
      isDragging.value = true;
      document.addEventListener('mousemove', onDrag);
      document.addEventListener('mouseup', stopDrag);
    };

    const onDrag = (e) => {
      if (isDragging.value) {
        const backgroundRect = document.querySelector('.slider-background').getBoundingClientRect();
        let newPosition = e.clientX - backgroundRect.left - 25; // 25是滑块的宽度的一半
        newPosition = Math.max(0, Math.min(newPosition, backgroundRect.width - 50)); // 限制在边界内
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
      targetPosition.value = Math.floor(Math.random() * (300 - 50)); // 重新随机目标位置
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
}

.slider {
  position: absolute;
  width: 50px;
  height: 100%;
  background-color: #007bff;
  cursor: pointer;
  transition: left 0.1s;
}

.mask {
  position: absolute;
  width: 50px;
  height: 100%;
  background-color: rgba(255, 0, 0, 0.5);
  pointer-events: none;
}
</style>
