<script setup lang="ts">
import { ref, onMounted } from 'vue';
import RandomEventLab from './components/RandomEventLab.vue'
import ProbabilityCalculator from './components/ProbabilityCalculator.vue'

// 页面加载动画
const isLoading = ref(true);

onMounted(() => {
  // 模拟页面加载完成
  setTimeout(() => {
    isLoading.value = false;
  }, 800);
});
</script>

<template>
  <!-- 加载动画 -->
  <div class="loading-overlay" v-if="isLoading">
    <div class="loading-spinner"></div>
  </div>

  <div class="app-container" :class="{ 'loaded': !isLoading }">
    <header class="app-header">
      <div class="header-content">
        <h1>随机事件探索器</h1>
        <p class="subtitle">探索概率世界的奥秘</p>
      </div>
    </header>

    <main class="app-main">
      <div class="container">
        <!-- 在大屏幕上使用并排布局 -->
        <div class="content-wrapper">
          <RandomEventLab />
          <ProbabilityCalculator />
        </div>
      </div>
    </main>

    <footer class="app-footer">
      <div class="container">
        <p>随机事件探索器 &copy; {{ new Date().getFullYear() }}</p>
        <p class="footer-links">
          <a href="#" title="关于我们">关于</a>
          <a href="#" title="使用帮助">帮助</a>
          <a href="#" title="联系我们">联系</a>
        </p>
      </div>
    </footer>
  </div>
</template>

<style scoped>
/* 加载动画 */
.loading-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: var(--bg-color);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
}

.loading-spinner {
  width: 50px;
  height: 50px;
  border: 5px solid rgba(58, 134, 255, 0.2);
  border-radius: 50%;
  border-top-color: var(--primary-color);
  animation: spin 1s ease-in-out infinite;
}

@keyframes spin {
  to { transform: rotate(360deg); }
}

.app-container {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  opacity: 0;
  transform: translateY(20px);
  transition: opacity 0.5s ease, transform 0.5s ease;
}

.app-container.loaded {
  opacity: 1;
  transform: translateY(0);
}

/* 页眉样式 */
.app-header {
  padding: 2rem 0;
  text-align: center;
  background-image: linear-gradient(135deg, var(--primary-color), var(--secondary-color));
  color: white;
  box-shadow: var(--shadow-md);
  margin-bottom: 2rem;
}

.header-content {
  max-width: 900px;
  margin: 0 auto;
  padding: 0 1rem;
}

.app-header h1 {
  margin: 0;
  font-size: 2.5rem;
  color: white;
  -webkit-text-fill-color: white;
}

.subtitle {
  margin-top: 0.5rem;
  font-size: 1.2rem;
  opacity: 0.9;
}

/* 主内容区域 */
.app-main {
  flex: 1;
}

.container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 1rem;
}

.content-wrapper {
  display: grid;
  gap: 2rem;
  margin-bottom: 2rem;
}

/* 页脚样式 */
.app-footer {
  background-color: var(--bg-card);
  color: var(--text-light);
  padding: 1.5rem 0;
  margin-top: 2rem;
  box-shadow: 0 -2px 10px rgba(0, 0, 0, 0.05);
  text-align: center;
}

.footer-links {
  margin-top: 0.5rem;
}

.footer-links a {
  margin: 0 0.5rem;
  color: var(--primary-color);
}

/* 响应式布局 */
@media (min-width: 992px) {
  .content-wrapper {
    grid-template-columns: 1fr 1fr;
  }
}

@media (max-width: 991px) {
  .content-wrapper {
    grid-template-columns: 1fr;
  }
}
</style>
