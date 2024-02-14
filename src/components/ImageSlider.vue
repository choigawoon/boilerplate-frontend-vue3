<template>
  <div class="slider-container relative w-full h-full max-w-xl mx-auto overflow-hidden border-4 border-dashed border-slate-500">
    <div class="absolute w-full h-full">
      <img :src="beforeImage" class="w-full h-full object-cover" />
    </div>
    <div class="absolute w-full h-full" :style="{ 'clip-path': 'polygon(0 0, ' + sliderPosition + '% 0, ' + sliderPosition + '% 100%, 0% 100%)' }">
      <img :src="afterImage" class="w-full h-full object-cover" />
    </div>
    <div class="slider" @mousedown="dragStart" @touchstart="dragStart" :style="{ left: sliderPosition + '%' }"></div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue';

// Props 정의
const props = defineProps({
  beforeImage: String,
  afterImage: String,
});

// 반응형 데이터
const dragging = ref(false);
const sliderPosition = ref(50); // 초기 슬라이더 위치를 가운데로 설정

// 드래그 시작 및 종료 핸들러
const dragStart = () => {
  dragging.value = true;
  window.addEventListener('mouseup', dragEnd);
  window.addEventListener('touchend', dragEnd);
};

const dragEnd = () => {
  dragging.value = false;
  window.removeEventListener('mouseup', dragEnd);
  window.removeEventListener('touchend', dragEnd);
};

const handleMouseMove = (event: MouseEvent) => {
  console.log('handleMouseMove')
  if (dragging.value) {
    const sliderContainer = event.currentTarget as HTMLElement;
    const offsetX = event.clientX - sliderContainer.getBoundingClientRect().left;
    sliderPosition.value = (offsetX / sliderContainer.offsetWidth) * 100;
    sliderPosition.value = Math.max(0, Math.min(100, sliderPosition.value));
  }
};

const handleTouchMove = (event: TouchEvent) => {
  console.log('handleTouchMove')
  if (dragging.value) {
    const touch = event.touches[0];
    const sliderContainer = event.currentTarget as HTMLElement;
    const offsetX = touch.clientX - sliderContainer.getBoundingClientRect().left;
    sliderPosition.value = (offsetX / sliderContainer.offsetWidth) * 100;
    sliderPosition.value = Math.max(0, Math.min(100, sliderPosition.value));
  }
};

// 마우스 및 터치 이벤트 핸들러를 적용하기 위한 이벤트 리스너 추가
onMounted(() => {
  const sliderContainer = document.querySelector('.slider-container');
  if (sliderContainer) {
    console.log('onMounted, slider-container found', sliderContainer)
    sliderContainer.addEventListener('mousemove', handleMouseMove);
    sliderContainer.addEventListener('touchmove', handleTouchMove);
  }
});

onUnmounted(() => {
  const sliderContainer = document.querySelector('.slider-container');
  if (sliderContainer) {
    sliderContainer.removeEventListener('mousemove', handleMouseMove);
    sliderContainer.removeEventListener('touchmove', handleTouchMove);
  } else {
    console.log('onUnmounted, not found slider-container')
  }
});
</script>

<style scoped>
.slider-container {
  position: relative;
  width: 100%;
  height: 100%;
}
.slider {
  position: absolute;
  top: 0;
  bottom: 0;
  width: 2px;
  background-color: #fff;
  cursor: ew-resize;
}
</style>
