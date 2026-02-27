<template>
  <div
    id="nav-music"
    class="needEndHide"
    :class="{ playing: isPlaying, stretch: isStretch, 'dark-mode': isDarkMode }"
    @click="handleClick"
  >
    <div
      id="nav-music-hoverTips"
      @click.stop="musicToggle"
    >
      {{ hoverText }}
    </div>
    <div ref="aplayerRef"></div>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted, nextTick } from 'vue';
import APlayer from 'aplayer';
import 'aplayer/dist/APlayer.min.css';

const aplayerRef = ref(null);
const ap = ref(null);
const isPlaying = ref(false);
const isStretch = ref(false);
const isDarkMode = ref(false);
const hoverText = ref('播放音乐');

// 音乐配置
const musicConfig = {
  id: '13681647281',
  server: 'netease',
  type: 'playlist',
  volume: 0.8,
  meting_api: 'https://meting.050815.xyz/api?server=:server&type=:type&id=:id&auth=:auth&r=:r'
};

// 检测当前主题
const checkTheme = () => {
  const theme = localStorage.getItem('theme');
  const hasDarkClass = document.documentElement.classList.contains('dark-theme');
  isDarkMode.value = theme === 'dark' || hasDarkClass;
};

// 监听主题变化
const observeTheme = () => {
  // 监听 localStorage 变化
  const originalSetItem = localStorage.setItem;
  localStorage.setItem = function(key, value) {
    originalSetItem.apply(this, arguments);
    if (key === 'theme') {
      checkTheme();
    }
  };

  // 监听 body 的 theme 属性变化
  const observer = new MutationObserver((mutations) => {
    mutations.forEach((mutation) => {
      if (mutation.type === 'attributes' && mutation.attributeName === 'theme') {
        checkTheme();
      }
    });
  });

  observer.observe(document.body, {
    attributes: true,
    attributeFilter: ['theme']
  });

  // 监听 html 的 class 变化
  const htmlObserver = new MutationObserver((mutations) => {
    mutations.forEach((mutation) => {
      if (mutation.type === 'attributes' && mutation.attributeName === 'class') {
        checkTheme();
      }
    });
  });

  htmlObserver.observe(document.documentElement, {
    attributes: true,
    attributeFilter: ['class']
  });

  return () => {
    observer.disconnect();
    htmlObserver.disconnect();
    localStorage.setItem = originalSetItem;
  };
};

// 获取音乐数据
const fetchMusicData = async () => {
  const apiUrl = musicConfig.meting_api
    .replace(':server', musicConfig.server)
    .replace(':type', musicConfig.type)
    .replace(':id', musicConfig.id)
    .replace(':auth', '')
    .replace(':r', Date.now());

  try {
    const response = await fetch(apiUrl);
    const data = await response.json();
    return data.map(song => ({
      name: song.title || song.name,
      artist: song.author || song.artist,
      url: song.url,
      cover: song.pic,
      lrc: song.lrc
    }));
  } catch (error) {
    console.error('获取音乐数据失败:', error);
    return [];
  }
};

// 切换播放/暂停
const musicToggle = () => {
  if (ap.value) {
    ap.value.toggle();
    isPlaying.value = !isPlaying.value;
    hoverText.value = isPlaying.value ? '暂停音乐' : '播放音乐';
  }
};

// 处理点击事件
const handleClick = (e) => {
  // 如果点击的是控制按钮，不处理
  if (e.target.closest('.aplayer-button')) {
    return;
  }
  // 切换展开状态
  if (isPlaying.value) {
    isStretch.value = !isStretch.value;
  }
};

let cleanupObserver = null;

onMounted(async () => {
  await nextTick();

  // 初始化主题检测
  checkTheme();
  // 开始监听主题变化
  cleanupObserver = observeTheme();

  const audioList = await fetchMusicData();

  if (audioList.length > 0 && aplayerRef.value) {
    ap.value = new APlayer({
      container: aplayerRef.value,
      audio: audioList,
      lrcType: 1,
      preload: 'none',
      mutex: true,
      volume: musicConfig.volume,
      order: 'random',
      mini: false,
      listFolded: true,
      theme: '#42A2FF'
    });

    // 监听播放状态
    ap.value.on('play', () => {
      isPlaying.value = true;
      hoverText.value = '暂停音乐';
    });

    ap.value.on('pause', () => {
      isPlaying.value = false;
      hoverText.value = '播放音乐';
    });

    // 初始为暂停状态
    isPlaying.value = false;
  }
});

onUnmounted(() => {
  if (ap.value) {
    ap.value.destroy();
  }
  if (cleanupObserver) {
    cleanupObserver();
  }
});
</script>

<style scoped>
/* CSS 变量 - 亮色模式 */
#nav-music {
  --liushen-nav-bg: rgba(255, 255, 255, 0.8);
  --liushen-nav-shadow: rgba(133, 133, 133, 0.6) 0px 5px 6px -5px;
  --liushen-card-bg: #fff;
  --liushen-card-border: 1px solid #e3e8f7;
  --liushen-text: #4c4948;
  --liushen-secondtext: #3c3c43cc;
  --default-bg-color: #42A2FF;
}

/* CSS 变量 - 深色模式 */
#nav-music.dark-mode {
  --liushen-nav-bg: rgba(18, 18, 18, 0.8);
  --liushen-nav-shadow: rgba(133, 133, 133, 0) 0px 5px 6px -5px;
  --liushen-card-bg: #2d2d2d;
  --liushen-card-border: 1px solid #42444a;
  --liushen-text: #ffffffb3;
  --liushen-secondtext: #a1a2b8;
}

@keyframes changeright {
  0%, 50%, 100% {
    transform: rotate(0deg) scale(1.1);
    box-shadow: 0 0 2px #ffffff00;
  }
  25%, 75% {
    transform: rotate(90deg) scale(1.1);
    box-shadow: 0 0 14px #ffffff;
  }
}

@keyframes playingShadow {
  0%, 100% {
    box-shadow: 0 0px 12px -3px #00000000;
  }
  50% {
    box-shadow: 0 0px 12px 0px var(--default-bg-color);
  }
}

@keyframes lightBar {
  0%, 100% {
    opacity: 0.1;
  }
  60% {
    opacity: 0.3;
  }
}

#nav-music {
  display: flex;
  align-items: center;
  position: fixed;
  z-index: 10000;
  bottom: 10px;
  left: 10px;
  cursor: pointer;
  transition: all 0.5s, left 0s;
  transform-origin: left bottom;
  box-shadow: var(--liushen-nav-shadow);
  border-radius: 40px;
  overflow: hidden;
  background: var(--liushen-card-bg);
}

#nav-music:active {
  transform: scale(0.97);
}

#nav-music.playing {
  border: var(--liushen-card-border);
  box-shadow: 0 0px 12px -3px #00000000;
  animation: playingShadow 5s linear infinite;
}

#nav-music.playing :deep(.aplayer.aplayer-withlrc .aplayer-pic) {
  box-shadow: 0 0 14px #ffffffa6;
  transform: rotate(0deg) scale(1.1);
  border-color: white;
  animation-play-state: running;
}

#nav-music.playing :deep(.aplayer.aplayer-withlrc .aplayer-info) {
  color: white;
}

#nav-music.playing #nav-music-hoverTips {
  width: 0;
}

#nav-music.playing :deep(.aplayer) {
  background: var(--default-bg-color);
  border: var(--liushen-card-border);
  backdrop-filter: saturate(180%) blur(20px);
  transform: translateZ(0);
}

#nav-music.playing :deep(.aplayer .aplayer-info .aplayer-controller .aplayer-bar-wrap .aplayer-bar .aplayer-played) {
  animation-play-state: running;
}

#nav-music-hoverTips {
  color: white;
  background: var(--default-bg-color);
  width: 100%;
  height: 100%;
  position: absolute;
  top: 0;
  left: 0;
  align-items: center;
  justify-content: center;
  display: flex;
  border-radius: 40px;
  opacity: 0;
  font-size: 12px;
  z-index: 2;
  transition: 0.3s;
  white-space: nowrap;
}

#nav-music:hover:not(.playing) #nav-music-hoverTips {
  opacity: 1;
}

:deep(.aplayer) {
  background: var(--liushen-card-bg);
  border-radius: 60px;
  height: 41px;
  display: flex;
  margin: 0;
  transition: 0.3s;
  border: var(--liushen-card-border);
  box-shadow: none;
  font-family: inherit;
}

:deep(.aplayer .aplayer-notice),
:deep(.aplayer .aplayer-miniswitcher),
:deep(.aplayer .aplayer-list) {
  display: none;
}

:deep(.aplayer .aplayer-body) {
  position: relative;
  display: flex;
  align-items: center;
  min-width: 180px;
}

:deep(.aplayer.aplayer-narrow .aplayer-body),
:deep(.aplayer.aplayer-narrow .aplayer-pic) {
  height: 66px;
  width: 66px;
}

:deep(.aplayer.aplayer-withlrc .aplayer-pic) {
  height: 25px;
  width: 25px;
  border-radius: 40px;
  z-index: 1;
  transition: 0.3s;
  transform: rotate(0deg) scale(1);
  border: var(--liushen-card-border);
  animation: changeright 24s linear infinite;
  animation-play-state: paused;
  margin-left: 8px;
}

:deep(.aplayer.aplayer-withlrc .aplayer-info) {
  height: 100%;
  color: var(--liushen-text);
  margin: 0;
  padding: 0;
  display: flex;
  align-items: center;
}

:deep(.aplayer .aplayer-info .aplayer-music) {
  margin: 0;
  display: flex;
  align-items: center;
  padding: 0 12px 0 8px;
  cursor: pointer;
  z-index: 1;
  height: 100%;
}

:deep(.aplayer .aplayer-info .aplayer-music .aplayer-title) {
  cursor: pointer;
  line-height: 1;
  display: inline-block;
  white-space: nowrap;
  max-width: 120px;
  overflow: hidden;
  text-overflow: ellipsis;
  transition: 0.3s;
  user-select: none;
}

:deep(.aplayer .aplayer-info .aplayer-controller) {
  position: absolute;
  width: 100%;
  height: 100%;
  top: 0;
  left: 0;
}

:deep(.aplayer .aplayer-info .aplayer-controller .aplayer-bar-wrap) {
  margin: 0;
  padding: 0;
}

:deep(.aplayer .aplayer-info .aplayer-controller .aplayer-bar-wrap .aplayer-bar) {
  height: 100%;
  background: transparent;
}

:deep(.aplayer .aplayer-info .aplayer-controller .aplayer-bar-wrap .aplayer-bar .aplayer-loaded) {
  display: none;
}

:deep(.aplayer .aplayer-info .aplayer-controller .aplayer-bar-wrap .aplayer-bar .aplayer-played) {
  height: 100%;
  opacity: 0.1;
  background-color: white !important;
  animation: lightBar 5s ease infinite;
  animation-play-state: paused;
}

:deep(.aplayer .aplayer-pic) {
  pointer-events: none;
}

:deep(.aplayer .aplayer-pic .aplayer-button) {
  bottom: 50%;
  right: 50%;
  transform: translate(50%, 50%);
  margin: 0;
  transition: 0.3s;
  pointer-events: all;
}

:deep(.aplayer .aplayer-pic:has(.aplayer-button.aplayer-play)) {
  animation-play-state: paused;
  transform: rotate(0deg) scale(1) !important;
}

:deep(.aplayer .aplayer-info .aplayer-controller .aplayer-time),
:deep(.aplayer .aplayer-info .aplayer-music .aplayer-author) {
  display: none;
}

:deep(.aplayer.aplayer-withlist .aplayer-info) {
  border: none;
}

:deep(.aplayer .aplayer-lrc) {
  width: 0;
  opacity: 0;
  transition: 0.3s;
  margin-bottom: -26px;
}

:deep(.aplayer .aplayer-lrc p.aplayer-lrc-current) {
  color: white;
  border: none;
  min-height: 20px;
  filter: none;
}

:deep(.aplayer .aplayer-lrc:after),
:deep(.aplayer .aplayer-lrc:before) {
  display: none;
}

:deep(.aplayer .aplayer-lrc p) {
  color: #ffffffb3;
  filter: blur(0.8px);
}

:deep(.aplayer .aplayer-thumb) {
  width: 0 !important;
  height: 0 !important;
}

@media screen and (min-width: 600px) {
  #nav-music.stretch :deep(.aplayer.aplayer-withlrc .aplayer-lrc) {
    width: 200px;
    margin-left: 8px;
    opacity: 1;
  }
}

@media screen and (max-width: 768px) {
  #nav-music {
    bottom: 10px;
    left: 10px;
  }

  :deep(.aplayer .aplayer-info .aplayer-music .aplayer-title) {
    max-width: 100px;
  }
}

@media screen and (max-width: 480px) {
  :deep(.aplayer .aplayer-body) {
    min-width: 150px;
  }

  :deep(.aplayer .aplayer-info .aplayer-music .aplayer-title) {
    max-width: 80px;
    font-size: 12px;
  }
}
</style>
