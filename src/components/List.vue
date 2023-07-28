<template>
  <div class="container">
    <div
      v-for="(item, index) in list"
      :key="index"
      class="item"
      :style="{ backgroundColor: item.background }"
    >
      {{ index }}
    </div>
    <div v-if="isLoading" class="loading">Loading...</div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, onBeforeUnmount } from 'vue';

const list = ref([{ background: "rgb(233,32,38)" }]);
const isLoading = ref(false);

const getList = (num: number) => {
  isLoading.value = true;
  // 0-5000ms的异步延迟
  setTimeout(() => {
    const newData = Array.from({ length: num }, () => ({
      background: getRandomColor(),
    }));
    list.value.push(...newData);
    isLoading.value = false;
  }, getRandomDelay());
};

//随机生成颜色
const getRandomColor = () => {
  const r = Math.floor(Math.random() * 256);
  const g = Math.floor(Math.random() * 256);
  const b = Math.floor(Math.random() * 256);
  return `rgb(${r},${g},${b})`;
};

//0-5000ms的延迟
const getRandomDelay = () => {
  return Math.floor(Math.random() * 5000); // [0, 5000) ms
};

//组件初始化后，调用 getList(10) 获取新的数据后渲染到页面上；
onMounted(() => {
  getList(10);

  // 监听滚动事件
  const handleScroll = () => {
    const container = document.querySelector('.container');
    const containerHeight = container?.scrollHeight ?? 0;
    const scrollTop = container?.scrollTop ?? 0;
    const clientHeight = container?.clientHeight ?? 0;
    const scrollBottom = containerHeight - scrollTop - clientHeight;

    // 滚动超过一半时，继续获取新数据
    if (scrollBottom <= containerHeight / 2 && list.value.length <= 50) {
      getList(1);
    }
  };

  window.addEventListener('scroll', handleScroll);

  // 组件销毁时移除事件监听
  onBeforeUnmount(() => {
    window.removeEventListener('scroll', handleScroll);
  });
});
</script>

<style>
.container {
  display: flex;
  flex-wrap: wrap;
}

.item {
  width: 100%;
  height: 500px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 24px;
  color: #fff;
}

.loading {
  width: 100%;
  height: 50px;
  line-height: 50px;
  text-align: center;
  background-color: #ddd;
}
</style>
