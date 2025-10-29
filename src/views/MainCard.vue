<template>
  <div class="mainCard animate-float">
    <div class="header">
      <div class="avatar" :emjoi="config.emjoi" @mouseenter="avatarHovered = true" @mouseleave="avatarHovered = false">
        <img :src="config.avatarUrl" alt="" :class="{ 'avatar-zoom': avatarHovered }" />
      </div>
      
      <!-- 主题切换按钮 - 滑块式设计 -->
      <div class="theme-toggle" @click="changeTheme">
        <div class="toggle-container">
          <div class="toggle-bg" :class="{ 'active': theme === 'dark' }">
            <!-- 太阳 -->
            <div class="sun"></div>
            
            <!-- 月亮 -->
            <div class="moon"></div>
            
            <!-- 星星 -->
            <div class="stars">
              <div class="star"></div>
              <div class="star"></div>
              <div class="star"></div>
            </div>
            
            <!-- 云朵 -->
            <div class="clouds">
              <div class="cloud"></div>
              <div class="cloud"></div>
            </div>
            
            <!-- 滑块 -->
            <div class="toggle-handle" :class="{ 'active': theme === 'dark' }"></div>
          </div>
        </div>
      </div>

      <div class="sayHi">
        <h1>
          Hi, I'm
          <span class="name" :data-text="config.name">
            {{ config.name }}
          </span>
        </h1>

        <div class="infoTags">
          <div v-if="config.infoTags.sex === '男'" class="tag hover">
            <Icon icon="ep:user" width="16" height="16" class="tag-icon" />
            <span class="boy"> ♂ </span>
          </div>
          <div v-else-if="config.infoTags.sex === '女'" class="tag hover">
            <Icon icon="ep:user" width="16" height="16" class="tag-icon" />
            <span class="girl"> ♀ </span>
          </div>
          <div v-else class="tag hover">
            <Icon icon="ep:user" width="16" height="16" class="tag-icon" />
            {{ config.infoTags.sex }}
          </div>
          <div class="tag hover">
            <Icon icon="ep:location" width="16" height="16" class="tag-icon" />
            {{ config.infoTags.province }}
          </div>
          <div class="tag hover">
            <Icon icon="ep:school" width="16" height="16" class="tag-icon" />
            {{ config.infoTags.company }}
          </div>
          <div class="tag hover">
            <Icon icon="ep:chat-square" width="16" height="16" class="tag-icon" />
            {{ config.infoTags.qq }}
          </div>
        </div>
      </div>
    </div>

    <div class="content">
      <div class="leftBox">
        <!-- todo -->
        <div class="card">
          <span class="cardHeader">我的一些鸽子计划📃</span>
          <div class="cardMain">
            <div class="todoList">
              <div
                class="todoItem"
                v-for="(i, index) in todo.todoList"
                :key="index"
              >
                <Icon
                  :icon="i.checked ? 'lets-icons:check-ring' : 'gg:radio-check'"
                  width="24"
                  height="24"
                />
                <span v-if="i.checked">
                  <del>{{ i.text }}</del>
                </span>
                <span v-else>
                  {{ i.text }}
                </span>
              </div>
            </div>
          </div>
        </div>

        <!-- 时间显示 -->
        <div class="card">
          <div class="time-progress">
            <h3>时光⌛</h3>
            <div class="progress-item">
              <p>☀️今天已经过去了 {{ hoursPassed }} / 24 小时</p>
              <div class="progress-bar">
                <div
                  class="progress-fill"
                  :style="{ width: hoursProgress + '%' }"
                ></div>
              </div>
            </div>

            <div class="progress-item">
              <p>📆本周已经过去了 {{ daysInWeekPassed }} / 7 天</p>
              <div class="progress-bar">
                <div
                  class="progress-fill"
                  :style="{ width: weekProgress + '%' }"
                ></div>
              </div>
            </div>

            <div class="progress-item">
              <p>
                🌙本月已经过去了 {{ daysInMonthPassed }} /
                {{ daysInCurrentMonth }} 天
              </p>
              <div class="progress-bar">
                <div
                  class="progress-fill"
                  :style="{ width: monthProgress + '%' }"
                ></div>
              </div>
            </div>

            <div class="progress-item">
              <p>
                ⭐今年已经过去了 {{ daysInYearPassed }} /
                {{ daysInCurrentYear }} 天
              </p>
              <div class="progress-bar">
                <div
                  class="progress-fill"
                  :style="{ width: yearProgress + '%' }"
                ></div>
              </div>
            </div>
          </div>
        </div>
      </div>

      <div class="rightBox">
        <div class="card">
          <p>你好鸭，很高兴认识你👋</p>
          <p>
            我叫
            <b>{{ config.name }}</b>
            （ {{ config.age }}年的 <b class="zodiac">{{ config.zodiac }}</b> ）
          </p>
          <p>
            是一名
            <span v-for="(i, index) in config.professions" :key="index">
              <b>{{ i }}</b>
              <span v-if="index < config.professions.length - 1">、</span>
            </span>
          </p>

          <!-- 技术栈 -->
          <h3>我的一些技术栈🫡</h3>
          <div class="techStack">
            <div
              v-for="(i, index) in techStack.techStack"
              :key="index"
              class="techItem"
              :data-name="i.name"
            >
              <Icon :icon="i.icon" width="40" height="40" />
            </div>
          </div>
        </div>

        <div class="typew card">
          <Icon icon="carbon:quotes" width="16" height="16" />
          <Typewriter :text="typewriter" />
          <Icon icon="ph:quotes-fill" width="16" height="16" />
        </div>

        <!-- 外链按钮 -->
        <div class="linkBox card">
          <link-btn
            v-for="(i, index) in linkBtns.linkBtn"
            :key="index"
            :icon="i.icon"
            :text="i.text"
            :color="i.color"
            :url="i.url"
          ></link-btn>
        </div>
      </div>
    </div>

    <div class="footer">
      <p>
        ©2025 麦希屿
      </p>
    </div>
  </div>
</template>

<script setup>
import config from "../config/config.json";
import linkBtns from "../config/linkBtn.json";
import techStack from "../config/techStack.json";
import todo from "../config/todo.json";
import typewriter from "../config/typewriter.json";
import { Icon } from "@iconify/vue";
import LinkBtn from "../components/LinkBtn.vue";
import { onMounted, ref, computed, nextTick } from "vue";
import Typewriter from "../components/Typewriter.vue";

const now = ref(new Date());

// 主题状态
const theme = ref(localStorage.getItem('theme') || 'light');

// 交互状态
const avatarHovered = ref(false);
const hoveredTech = ref(-1);
const hoveredLink = ref(-1);
const skillProgress = ref([]);

// 进度数据（添加动画效果）
const hoursPassed = computed(() => now.value.getHours());
const hoursProgress = computed(() =>
  ((hoursPassed.value / 24) * 100).toFixed(2)
);

const daysInWeekPassed = computed(() => {
  const day = now.value.getDay();
  return day === 0 ? 7 : day;
});
const weekProgress = computed(() =>
  ((daysInWeekPassed.value / 7) * 100).toFixed(2)
);

const daysInMonthPassed = computed(() => now.value.getDate());
const daysInCurrentMonth = computed(() =>
  new Date(now.value.getFullYear(), now.value.getMonth() + 1, 0).getDate()
);
const monthProgress = computed(
  () => (daysInMonthPassed.value / daysInCurrentMonth.value) * 100
);

const daysInYearPassed = computed(() => {
  const startOfYear = new Date(now.value.getFullYear(), 0, 1);
  const diff = now.value - startOfYear;
  return Math.ceil(diff / (1000 * 60 * 60 * 24));
});

const daysInCurrentYear = computed(() => {
  const isLeap = isLeapYear(now.value.getFullYear());
  return isLeap ? 366 : 365;
});

const yearProgress = computed(
  () => (daysInYearPassed.value / daysInCurrentYear.value) * 100
);

// 数字增长动画
const animateNumber = (start, end, duration, callback) => {
  const startTime = performance.now();
  const range = end - start;
  
  const updateNumber = (currentTime) => {
    const elapsed = currentTime - startTime;
    const progress = Math.min(elapsed / duration, 1);
    // 使用缓动函数
    const easeOutQuad = progress * (2 - progress);
    const current = Math.floor(start + range * easeOutQuad);
    
    callback(current);
    
    if (progress < 1) {
      requestAnimationFrame(updateNumber);
    }
  };
  
  requestAnimationFrame(updateNumber);
};

function isLeapYear(year) {
  return (year % 4 === 0 && year % 100 !== 0) || year % 400 === 0;
}

// 初始化主题
const initTheme = () => {
  if (theme.value === 'dark') {
    document.documentElement.classList.add('dark-theme');
    document.body.setAttribute('theme', 'dark');
  }
};

// 切换主题
const changeTheme = () => {
  theme.value = theme.value === 'light' ? 'dark' : 'light';
  localStorage.setItem('theme', theme.value);
  
  if (theme.value === 'dark') {
    document.documentElement.classList.add('dark-theme');
    document.body.setAttribute('theme', 'dark');
  } else {
    document.documentElement.classList.remove('dark-theme');
    document.body.setAttribute('theme', 'light');
  }
};

// 初始化动画
const initAnimations = () => {
  // 为技术栈项初始化进度值
  skillProgress.value = techStack.techStack.map(() => 0);
  
  nextTick(() => {
    // 为每个技术栈项设置随机进度并执行动画
    techStack.techStack.forEach((tech, index) => {
      const targetProgress = 60 + Math.floor(Math.random() * 40); // 60-100之间的随机值
      animateNumber(0, targetProgress, 2000, (value) => {
        skillProgress.value[index] = value;
      });
    });
  });
};

onMounted(() => {
  // 初始化主题
  initTheme();
  
  setInterval(() => {
    now.value = new Date();
  }, 1000);
  
  // 初始化动画
  initAnimations();
});
</script>

<style>
@import url(../assets/css/MainCard.css);

/* 主题切换按钮 - 滑块式设计 */
.header {
  position: relative;
}

.theme-toggle {
  position: absolute;
  top: 10px;
  right: 10px;
  cursor: none;
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 10;
  padding: 5px;
  transition: all 0.3s ease;
  border-radius: 50px;
}

.theme-toggle:hover {
  transform: scale(1.05);
}

.toggle-container {
  display: flex;
  justify-content: center;
  align-items: center;
}

.toggle-bg {
  position: relative;
  width: 60px;
  height: 32px;
  border-radius: 20px;
  background: linear-gradient(135deg, #ffdd77, #ffbb55);
  transition: all 0.6s cubic-bezier(0.4, 0, 0.2, 1);
  overflow: hidden;
  box-shadow: 
    0 4px 15px rgba(255, 221, 119, 0.3),
    inset 0 2px 5px rgba(255, 255, 255, 0.3),
    inset 0 -2px 5px rgba(0, 0, 0, 0.1);
}

.toggle-bg.active {
  background: linear-gradient(135deg, #2c3e50, #34495e);
  box-shadow: 
    0 4px 15px rgba(44, 62, 80, 0.3),
    inset 0 2px 5px rgba(255, 255, 255, 0.05),
    inset 0 -2px 5px rgba(0, 0, 0, 0.3);
}

/* 太阳 */
.sun {
  position: absolute;
  top: 4px;
  left: 4px;
  width: 24px;
  height: 24px;
  border-radius: 50%;
  background: #ffd700;
  box-shadow: 0 0 15px #ffd700;
  transition: all 0.6s cubic-bezier(0.4, 0, 0.2, 1);
  opacity: 1;
}

.toggle-bg.active .sun {
  transform: translateX(28px) scale(0.6);
  opacity: 0.3;
}

/* 月亮 */
.moon {
  position: absolute;
  top: 4px;
  right: 4px;
  width: 24px;
  height: 24px;
  border-radius: 50%;
  background: transparent;
  border: 3px solid #e0e0e0;
  transform: translateX(28px) scale(0.6);
  opacity: 0;
  transition: all 0.6s cubic-bezier(0.4, 0, 0.2, 1);
}

.toggle-bg.active .moon {
  transform: translateX(0) scale(1);
  opacity: 1;
  box-shadow: 0 0 10px rgba(224, 224, 224, 0.6);
}

/* 星星 */
.stars {
  position: absolute;
  width: 100%;
  height: 100%;
  opacity: 0;
  transition: opacity 0.6s ease;
}

.toggle-bg.active .stars {
  opacity: 1;
}

.star {
  position: absolute;
  background: #ffffff;
  border-radius: 50%;
  animation: twinkle 2s infinite ease-in-out;
}

.star:nth-child(1) {
  width: 2px;
  height: 2px;
  top: 8px;
  right: 18px;
  animation-delay: 0s;
}

.star:nth-child(2) {
  width: 1.5px;
  height: 1.5px;
  top: 15px;
  right: 25px;
  animation-delay: 0.5s;
}

.star:nth-child(3) {
  width: 1px;
  height: 1px;
  top: 20px;
  right: 12px;
  animation-delay: 1s;
}

@keyframes twinkle {
  0%, 100% {
    opacity: 0.4;
    transform: scale(1);
  }
  50% {
    opacity: 1;
    transform: scale(1.2);
  }
}

/* 云朵 */
.clouds {
  position: absolute;
  width: 100%;
  height: 100%;
  opacity: 0.7;
  transition: opacity 0.6s ease;
}

.toggle-bg.active .clouds {
  opacity: 0;
}

.cloud {
  position: absolute;
  background: rgba(255, 255, 255, 0.7);
  border-radius: 10px;
  animation: float 8s infinite ease-in-out;
}

.cloud:nth-child(1) {
  width: 10px;
  height: 5px;
  top: 10px;
  left: 35px;
  animation-delay: 0s;
}

.cloud:nth-child(2) {
  width: 12px;
  height: 6px;
  top: 8px;
  left: 50px;
  animation-delay: 2s;
}

@keyframes float {
  0%, 100% {
    transform: translateX(0) translateY(0);
  }
  50% {
    transform: translateX(-5px) translateY(-2px);
  }
}

/* 滑块 */
.toggle-handle {
  position: absolute;
  top: 3px;
  left: 3px;
  width: 26px;
  height: 26px;
  border-radius: 50%;
  background: white;
  box-shadow: 
    0 2px 5px rgba(0, 0, 0, 0.2),
    0 0 10px rgba(255, 255, 255, 0.5);
  transition: all 0.6s cubic-bezier(0.4, 0, 0.2, 1);
  z-index: 10;
}

.toggle-handle.active {
  transform: translateX(28px);
  background: linear-gradient(135deg, #f0f0f0, #e0e0e0);
}

/* 响应式设计 */
@media (max-width: 768px) {
  .theme-toggle {
    top: 8px;
    right: 8px;
  }
  
  .toggle-bg {
    width: 56px;
    height: 30px;
  }
  
  .toggle-handle {
    width: 24px;
    height: 24px;
  }
  
  .toggle-handle.active {
    transform: translateX(26px);
  }
  
  .sun, .moon {
    width: 22px;
    height: 22px;
  }
}

@media (max-width: 480px) {
  .theme-toggle {
    top: 5px;
    right: 5px;
  }
  
  .toggle-bg {
    width: 52px;
    height: 28px;
  }
  
  .toggle-handle {
    width: 22px;
    height: 22px;
  }
  
  .toggle-handle.active {
    transform: translateX(24px);
  }
}

/* 增强动画效果 */
.mainCard {
  transition: all 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275);
}

.mainCard:hover {
  transform: translateY(-5px);
  box-shadow: 0 16px 48px rgba(0, 0, 0, 0.15);
}

.animate-float {
  animation: float 6s ease-in-out infinite;
}

@keyframes float {
  0% {
    transform: translateY(0px);
  }
  50% {
    transform: translateY(-10px);
  }
  100% {
    transform: translateY(0px);
  }
}

.avatar {
  transition: all 0.3s ease;
}

.avatar:hover {
  transform: scale(1.05) rotate(2deg);
}

.avatar img {
  transition: transform 0.5s ease;
}

.avatar-zoom {
  transform: scale(1.1);
}

.todoItem {
  transition: all 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275);
  cursor: pointer;
  position: relative;
  overflow: hidden;
}

.todoItem:hover {
  transform: translateX(8px) scale(1.02);
  background-color: rgba(255, 255, 255, 0.9);
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.08);
}

.tag.hover {
  transition: all 0.3s ease;
  cursor: pointer;
  position: relative;
  overflow: hidden;
}

.tag.hover:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
  background-color: rgba(255, 255, 255, 0.9);
}

.progress-fill {
  transition: width 1s ease;
  position: relative;
  overflow: hidden;
}

.progress-fill::after {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: linear-gradient(90deg, transparent, rgba(255, 255, 255, 0.3), transparent);
  animation: shimmer 2s infinite;
}

@keyframes shimmer {
  0% {
    transform: translateX(-100%);
  }
  100% {
    transform: translateX(100%);
  }
}

.techItem {
  transition: all 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275);
  cursor: pointer;
  position: relative;
  overflow: hidden;
}

.techItem:hover {
  transform: translateY(-5px) scale(1.05);
  box-shadow: 0 6px 20px rgba(0, 0, 0, 0.1);
}

.techItem:hover .iconify {
  transform: scale(1.2) rotate(5deg);
}

/* 技能进度条 */
.skill-progress {
  height: 8px;
  background-color: rgba(0, 0, 0, 0.1);
  border-radius: 4px;
  margin-top: 8px;
  overflow: hidden;
  position: relative;
}

.skill-progress-fill {
  height: 100%;
  background: linear-gradient(90deg, #3498db, #60a5fa);
  border-radius: 4px;
  transition: width 1.5s ease-out;
  position: relative;
}

.skill-progress-fill::after {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: linear-gradient(90deg, transparent, rgba(255, 255, 255, 0.3), transparent);
  animation: shimmer 2s infinite;
}

/* 响应式优化 */
@media (max-width: 768px) {
  .mainCard:hover {
    transform: translateY(-3px);
  }
  
  .todoItem:hover,
  .techItem:hover {
    transform: none;
  }
}
</style>
