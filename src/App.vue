<script setup lang="ts">
import { ref } from 'vue'

const videoUrl = ref('')
const iframeUrl = ref('')
const showPlayer = ref(false)

const videoSites = [
  { id: 111, name: '爱奇艺', url: 'https://www.iqiyi.com', color: '#E47833' },
  { id: 222, name: '腾讯视频', url: 'https://v.qq.com', color: '#6B8E23' },
  { id: 333, name: '优酷视频', url: 'https://www.youku.com', color: '#3299CC' },
  // { id: 444, name: '哔哩哔哩', url: 'https://www.bilibili.com', color: '#FB7299' },
]

const resolverUrls = [
  { id: 666, name: '解析站1', url: 'https://jx.xmflv.com/?url=' },
  { id: 777, name: '解析站2', url: 'https://www.ckmov.com/?url=' },
  { id: 888, name: '解析站3', url: 'https://www.pangujiexi.com/?url=' },
]

const selectedResolver = ref(0)

function openSite(url: string) {
  window.open(url, '_blank')
}

function playVideo() {
  if (!videoUrl.value.trim()) return
  
  const resolver = resolverUrls[selectedResolver.value]
  iframeUrl.value = resolver.url + encodeURIComponent(videoUrl.value.trim())
  showPlayer.value = true
}

function openResolverDirectly() {
  if (!videoUrl.value.trim()) return
  
  const resolver = resolverUrls[selectedResolver.value]
  window.open(resolver.url + encodeURIComponent(videoUrl.value.trim()), '_blank')
}

function closePlayer() {
  showPlayer.value = false
  iframeUrl.value = ''
}

function clearUrl() {
  videoUrl.value = ''
}
</script>

<template>

  <div class="app-container">
    <header class="header">
      <h1>🎬 VIP追剧神器</h1>
      <p class="subtitle">一键解析各大视频网站VIP内容</p>
    </header>

    <main class="main-content">
      <section class="input-section">
        <div class="input-group">
          <!-- 输入框，用于输入视频网址，重点了解一下@keyup.enter事件 -->
           <!-- 就是点击enter和点击按钮的效果是一样的 -->
          <el-input
            v-model="videoUrl"
            placeholder="请输入视频网址"
            class="url-input"
            @keyup.enter="playVideo"
          />
          <!-- 涉及到的知识点是:disabled -->
          <!-- 点击播放按钮时，如果输入框为空，按钮会禁用 -->
          <el-button type="primary" @click="playVideo" :disabled="!videoUrl.trim()" class="play-btn">
            播放视频
          </el-button>
          <el-button @click="clearUrl" class="clear-btn">清空</el-button>
        </div>

        <div class="resolver-select">
          <span class="label">选择解析站点：</span>
          <el-radio-group v-model="selectedResolver">
            <!-- 涉及到v-for的知识点，这个key一定要使用每一个节点的特殊值 -->
            <el-radio v-for="(resolver, index) in resolverUrls" :key="resolver.id" :value="index" :disabled="index > 0">
              {{ resolver.name }}
            </el-radio>
          </el-radio-group>
          <el-button type="text" @click="openResolverDirectly" :disabled="!videoUrl.trim()">
            在新窗口打开
          </el-button>
        </div>
      </section>

      <section class="sites-section">
        <h2>视频网站快捷入口</h2>
        <div class="sites-grid">
          <!-- 这个就是一个内置的自定义style属性 -->
          <el-button
            v-for="site in videoSites"
            :key="site.id"
            
            :style="{ '--site-color': site.color }"
            class="site-btn"
            @click="openSite(site.url)"
          >
            <!-- <span class="site-icon">{{ site.name.charAt(0) }}</span> -->
            <span class="site-name">{{ site.name }}</span>
          </el-button>
        </div>
      </section>


    </main>

    <footer class="footer">
      <p>提示：本案例仅供学习使用，不可作为他用。</p>
    </footer>

    <div v-if="showPlayer" class="fullscreen-player">
      <div class="fullscreen-header">
        <h2>视频播放</h2>
        <el-button type="danger" @click="closePlayer" class="close-btn">关闭</el-button>
      </div>
      <div class="fullscreen-iframe">
        <iframe :src="iframeUrl" class="video-iframe" title="视频播放" sandbox="allow-same-origin allow-scripts allow-popups allow-forms"></iframe>
      </div>
    </div>
  </div>
</template>

<style scoped>
.app-container {
  min-height: 100vh;
  background: linear-gradient(135deg, #1a1a2e 0%, #16213e 50%, #0f3460 100%);
  padding: 20px;
}

.header {
  text-align: center;
  color: #fff;
  margin-bottom: 30px;
}

.header h1 {
  font-size: 2.5rem;
  margin-bottom: 10px;
  background: linear-gradient(90deg, #ff6b6b, #feca57, #48dbfb);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}

.subtitle {
  font-size: 1.1rem;
  color: rgba(255, 255, 255, 0.7);
}

.main-content {
  max-width: 1200px;
  margin: 0 auto;
}

.input-section {
  background: rgba(255, 255, 255, 0.1);
  backdrop-filter: blur(10px);
  border-radius: 16px;
  padding: 24px;
  margin-bottom: 24px;
}

.input-group {
  display: flex;
  gap: 12px;
  margin-bottom: 16px;
}

.url-input {
  flex: 1;
}

.play-btn {
  min-width: 120px;
}

.resolver-select {
  display: flex;
  align-items: center;
  gap: 16px;
  flex-wrap: wrap;
}

.label {
  color: rgba(255, 255, 255, 0.8);
  font-weight: 500;
}

.sites-section {
  background: rgba(255, 255, 255, 0.1);
  backdrop-filter: blur(10px);
  border-radius: 16px;
  padding: 24px;
  margin-bottom: 24px;
}

.sites-section h2 {
  color: #fff;
  margin-bottom: 20px;
  font-size: 1.3rem;
}

.sites-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(150px, 1fr));
  gap: 16px;
}

.site-btn {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 24px 16px;
  background: rgba(255, 255, 255, 0.1);
  border: 2px solid var(--site-color);
  border-radius: 12px;
  color: #fff;
  transition: all 0.3s ease;
}

.site-btn:hover {
  background: var(--site-color);
  transform: translateY(-4px);
  box-shadow: 0 8px 25px rgba(0, 0, 0, 0.3);
}

.site-icon {
  font-size: 2rem;
  margin-bottom: 8px;
}

.site-name {
  font-size: 1.5rem;
  font-weight: 500;
}

.player-section {
  background: rgba(255, 255, 255, 0.1);
  backdrop-filter: blur(10px);
  border-radius: 16px;
  padding: 24px;
}

.player-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 16px;
}

.player-header h2 {
  color: #fff;
  font-size: 1.3rem;
}

.close-btn {
  min-width: 80px;
}

.player-container {
  background: #000;
  border-radius: 12px;
  overflow: hidden;
  position: relative;
  padding-bottom: 56.25%;
  height: 0;
}

.video-iframe {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  border: none;
}

.footer {
  text-align: center;
  color: rgba(255, 255, 255, 0.6);
  margin-top: 40px;
  padding: 20px;
}

.fullscreen-player {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background: rgba(0, 0, 0, 0.95);
  z-index: 9999;
  display: flex;
  flex-direction: column;
}

.fullscreen-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 20px 30px;
  background: rgba(0, 0, 0, 0.8);
}

.fullscreen-header h2 {
  color: #fff;
  font-size: 1.3rem;
  margin: 0;
}

.fullscreen-header .close-btn {
  min-width: 80px;
}

.fullscreen-iframe {
  flex: 1;
  position: relative;
  background: #000;
}

.fullscreen-iframe .video-iframe {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
}

@media (max-width: 768px) {
  .input-group {
    flex-direction: column;
  }

  .play-btn, .clear-btn {
    width: 100%;
  }

  .header h1 {
    font-size: 1.8rem;
  }
}
</style>
