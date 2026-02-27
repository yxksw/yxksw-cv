<template>
  <div class="app" :class="{ 'dark': theme == 'dark' }">
    <!-- 粒子背景 -->
    <ParticlesBackground />
    <!-- 自定义鼠标光标效果 -->
    <CustomCursor />
    <!-- 主卡片内容 -->
    <main-card></main-card>
    <!-- 音乐胶囊播放器 -->
    <MusicCapsule />
  </div>
</template>

<script setup>
import MainCard from "./views/MainCard.vue";
import ParticlesBackground from './components/ParticlesBackground.vue';
import CustomCursor from './components/CustomCursor.vue';
import MusicCapsule from './components/MusicCapsule.vue';
import { ref, onMounted } from "vue";
import config from './config/config.json';

const theme = ref(config.theme || 'light');

onMounted(() => {
  // 鼠标移动监听已在CustomCursor组件中处理
});
</script>

<style>
@import url(assets/css/App.css);

/* 全局样式重置 */
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  cursor: none;
}

body {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, sans-serif;
  background-color: #f5f5f5;
  color: #333;
  transition: background-color 0.3s, color 0.3s;
  overflow-x: hidden;
}

/* 应用容器 */
.app {
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 20px;
  opacity: 1; /* 直接显示内容 */
  transition: background-color 0.3s ease, color 0.3s ease;
  position: relative;
}

.app.dark {
  background-color: #121212;
  color: #e0e0e0;
}

/* 鼠标跟随效果 */
.cursor-effect {
  position: fixed;
  width: 20px;
  height: 20px;
  border-radius: 50%;
  background: rgba(59, 130, 246, 0.3);
  pointer-events: none;
  transform: translate(-50%, -50%);
  transition: transform 0.3s ease, width 0.3s ease, height 0.3s ease;
  z-index: 9999;
  backdrop-filter: blur(4px);
  border: 2px solid rgba(59, 130, 246, 0.5);
}

/* 主题相关逻辑在MainCard组件中实现 */

/* 移除加载动画样式 */

/* 链接和按钮的自定义光标 */
a, button {
  position: relative;
  z-index: 10;
}

/* 响应式设计 */
@media (max-width: 768px) {
  .app {
    padding: 15px;
  }
  
  .theme-toggle {
    top: 15px;
    right: 15px;
    width: 45px;
    height: 45px;
    font-size: 18px;
  }
  
  .cursor-effect {
    display: none;
  }
  
  * {
    cursor: default;
  }
}

@media (max-width: 480px) {
  .app {
    padding: 10px;
  }
  
  .theme-toggle {
    top: 10px;
    right: 10px;
    width: 40px;
    height: 40px;
    font-size: 16px;
  }
}
</style>
