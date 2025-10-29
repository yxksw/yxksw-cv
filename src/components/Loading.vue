<template>
  <div class="loading-container">
    <div class="loading-spinner">
      <div class="spinner-circle"></div>
      <div class="spinner-dots">
        <span v-for="n in 3" :key="n" class="dot" :style="{ animationDelay: `${n * 200}ms` }"></span>
      </div>
    </div>
    <div class="loading-text">
      <span class="loading-char" v-for="(char, index) in loadingText" :key="index" :style="{ animationDelay: `${index * 100}ms` }">
        {{ char }}
      </span>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';

const loadingText = ref('加载中...');

onMounted(() => {
  // 根据主题调整加载动画颜色
  const updateLoadingColor = () => {
    const theme = document.body.getAttribute('theme');
    const spinner = document.querySelector('.spinner-circle');
    const dots = document.querySelectorAll('.dot');
    
    if (theme === 'dark') {
      spinner.style.borderTopColor = '#60a5fa';
      dots.forEach(dot => {
        dot.style.backgroundColor = '#60a5fa';
      });
    } else {
      spinner.style.borderTopColor = '#3498db';
      dots.forEach(dot => {
        dot.style.backgroundColor = '#3498db';
      });
    }
  };
  
  updateLoadingColor();
  
  // 监听主题变化
  const observer = new MutationObserver(mutations => {
    mutations.forEach(mutation => {
      if (mutation.attributeName === 'theme') {
        updateLoadingColor();
      }
    });
  });
  
  observer.observe(document.body, { attributes: true });
});
</script>

<style scoped>
.loading-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  height: 100vh;
  width: 100vw;
  position: fixed;
  top: 0;
  left: 0;
  z-index: 9999;
  background-color: rgba(255, 255, 255, 0.9);
  backdrop-filter: blur(5px);
  transition: opacity 0.3s ease;
}

:global(.theme-dark) .loading-container {
  background-color: rgba(18, 18, 18, 0.9);
}

.loading-spinner {
  position: relative;
  width: 80px;
  height: 80px;
  margin-bottom: 30px;
}

.spinner-circle {
  width: 80px;
  height: 80px;
  border: 4px solid rgba(52, 152, 219, 0.1);
  border-top: 4px solid #3498db;
  border-radius: 50%;
  animation: spin 1.2s cubic-bezier(0.5, 0, 0.5, 1) infinite;
}

.spinner-dots {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
}

.dot {
  width: 8px;
  height: 8px;
  border-radius: 50%;
  background-color: #3498db;
  margin: 0 3px;
  opacity: 0;
  animation: dotBounce 2s ease-in-out infinite;
}

@keyframes spin {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}

@keyframes dotBounce {
  0%, 80%, 100% {
    transform: scale(0) translateY(0);
    opacity: 0;
  }
  40% {
    transform: scale(1) translateY(-15px);
    opacity: 1;
  }
}

.loading-text {
  font-size: 1.2rem;
  font-weight: 600;
  color: #555;
  display: flex;
  gap: 4px;
}

:global(.theme-dark) .loading-text {
  color: #ddd;
}

.loading-char {
  opacity: 0;
  animation: fadeInUp 0.6s ease-out forwards;
  display: inline-block;
  transform: translateY(10px);
}

@keyframes fadeInUp {
  to {
    opacity: 1;
    transform: translateY(0);
  }
}
</style>
