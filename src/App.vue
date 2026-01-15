<script setup>
import { ref, computed, onMounted, onUnmounted, nextTick, watch } from 'vue';
import AOS from 'aos';
import 'aos/dist/aos.css';
import { timelineData } from './timelineData';
// --- 状态管理 ---
const currentFilter = ref('all');
const showLightbox = ref(false);
const currentImage = ref('');
// 选中项状态管理
const selectedItem = ref(null);
const showDetail = ref(false);
// 引言弹窗控制
const showIntroModal = ref(false);
// Canvas 引用
const bgCanvas = ref(null);

const processIntroLinks = (content) => {
  if (!content) return '';
  // 匹配 [数字] 标题 URL 格式
  const linkRegex = /\[(\d+)\]\s+([^\n]+?)\s+(https?:\/\/[^\s\n]+)/g;
  // 替换：标题转超链接，隐藏URL
  let processedContent = content.replace(linkRegex, (match, num, title, url) => {
    return `<a href="${url}" target="_blank" class="intro-link">[${num}] ${title}</a>`;
  });
  // 清理残留的独立URL
  processedContent = processedContent.replace(/https?:\/\/[^\s\n]+/g, '');
  return processedContent;
};

// 提取引言数据
const introData = computed(() => {
  const introItem = timelineData.find(item => item.year === "引言");
  return {
    title: introItem?.title || '',
    detail: processIntroLinks(introItem?.detail || '')
  };
});

// 关闭引言弹窗
const closeIntroModal = () => {
  showIntroModal.value = false;
};


// 2. 列表筛选逻辑 (排除 "引言" 项)
const filteredData = computed(() => {
  // 先排除引言，它只用于弹窗，不应出现在列表中
  const list = timelineData.filter(item => item.year !== "引言");

  if (currentFilter.value === 'all') {
    return list;
  }
  return list.filter(item => item.type === currentFilter.value);
});

// --- Canvas 背景特效逻辑 ---
let animationFrameId;

const initCanvas = () => {
  const canvas = bgCanvas.value;
  if (!canvas) return;

  const ctx = canvas.getContext('2d');
  let width, height;
  let particles = [];

  // 配置
  const particleCount = 60; // 粒子数量
  const connectionDistance = 150; // 连线距离
  const mouseDistance = 200; // 鼠标互动距离

  // 调整画布大小
  const resize = () => {
    width = canvas.width = window.innerWidth;
    height = canvas.height = window.innerHeight;
  };
  window.addEventListener('resize', resize);
  resize();

  // 鼠标位置追踪
  let mouse = { x: null, y: null };
  window.addEventListener('mousemove', (e) => {
    mouse.x = e.clientX;
    mouse.y = e.clientY;
  });
  window.addEventListener('mouseout', () => {
    mouse.x = null;
    mouse.y = null;
  });

  // 粒子类
  class Particle {
    constructor() {
      this.x = Math.random() * width;
      this.y = Math.random() * height;
      this.vx = (Math.random() - 0.5) * 1.5; // 速度略微调快
      this.vy = (Math.random() - 0.5) * 1.5;
      this.size = Math.random() * 2 + 1;
    }

    update() {
      this.x += this.vx;
      this.y += this.vy;

      // 边界反弹
      if (this.x < 0 || this.x > width) this.vx *= -1;
      if (this.y < 0 || this.y > height) this.vy *= -1;

      // 鼠标互动（排斥效果）
      if (mouse.x != null) {
        let dx = mouse.x - this.x;
        let dy = mouse.y - this.y;
        let distance = Math.sqrt(dx * dx + dy * dy);
        if (distance < mouseDistance) {
          const forceDirectionX = dx / distance;
          const forceDirectionY = dy / distance;
          const force = (mouseDistance - distance) / mouseDistance;
          const directionX = forceDirectionX * force * 3;
          const directionY = forceDirectionY * force * 3;
          this.x -= directionX;
          this.y -= directionY;
        }
      }
    }

    draw() {
      // 粒子颜色：半透明白色
      ctx.fillStyle = 'rgba(255, 255, 255, 0.4)';
      ctx.beginPath();
      ctx.arc(this.x, this.y, this.size, 0, Math.PI * 2);
      ctx.fill();
    }
  }

  // 初始化粒子
  for (let i = 0; i < particleCount; i++) {
    particles.push(new Particle());
  }

  // 动画循环
  const animate = () => {
    ctx.clearRect(0, 0, width, height);

    // 更新和绘制
    for (let i = 0; i < particles.length; i++) {
      particles[i].update();
      particles[i].draw();

      // 连线逻辑
      for (let j = i; j < particles.length; j++) {
        let dx = particles[i].x - particles[j].x;
        let dy = particles[i].y - particles[j].y;
        let distance = Math.sqrt(dx * dx + dy * dy);

        if (distance < connectionDistance) {
          ctx.beginPath();
          // 线条透明度随距离变化
          let opacity = 1 - (distance / connectionDistance);
          ctx.strokeStyle = `rgba(255, 255, 255, ${opacity * 0.2})`;
          ctx.lineWidth = 1;
          ctx.moveTo(particles[i].x, particles[i].y);
          ctx.lineTo(particles[j].x, particles[j].y);
          ctx.stroke();
        }
      }
    }
    animationFrameId = requestAnimationFrame(animate);
  };

  animate();
};

// --- 方法 ---

const setFilter = (type) => {
  currentFilter.value = type;
  nextTick(() => {
    AOS.refresh();
  });
};

const openLightbox = (src) => {
  currentImage.value = src;
  showLightbox.value = true;
};

const closeLightbox = () => {
  showLightbox.value = false;
};

const handleCardClick = (item) => {
  selectedItem.value = item;
  showDetail.value = false;
  nextTick(() => {
    showDetail.value = true;
  });
};


// --- 生命周期 ---

onMounted(() => {
  // 1. 初始化 AOS
  AOS.init({
    duration: 800,
    once: false,
  });

  // 2. 初始化背景特效
  initCanvas();

  // 3. 弹窗逻辑

  showIntroModal.value = true;

});

onUnmounted(() => {
  // 清理动画和监听器，防止内存泄漏
  cancelAnimationFrame(animationFrameId);
  window.removeEventListener('resize', () => { });
  window.removeEventListener('mousemove', () => { });
  window.removeEventListener('mouseout', () => { });
});
// --- 侦听器 ---
// watch([showDetail, showIntroModal, showLightbox], ([detail, intro, lightbox]) => {
//   if (detail || intro || lightbox) {
//     document.body.style.overflow = 'hidden';
//   } else {
//     document.body.style.overflow = '';
//   }
// });
</script>

<template>
  <div class="app-bg">

    <canvas ref="bgCanvas" class="background-canvas"></canvas>

    <div class="main-content">

      <div class="app-container">
        <header class="hero">
          <div class="hero-content" data-aos="fade-down">
            <h1>4399 编年史</h1>
            <p class="subtitle">盗版发家 · 童年印记 · 兴衰参半</p>

            <div class="filter-box">
              <button v-for="btn in [
                { label: '全部', value: 'all' },
                { label: '发展历程', value: 'milestone' },
                { label: '技术革命', value: 'flash' },
                { label: '经典游戏', value: 'game' },
                { label: '遗忘英雄', value: 'true' }
              ]" :key="btn.value" class="filter-btn" :class="{ active: currentFilter === btn.value }"
                @click="setFilter(btn.value)">
                {{ btn.label }}
              </button>
            </div>
          </div>
        </header>

        <main class="timeline-container">
          <div v-for="(item, index) in filteredData" :key="index" class="card-wrapper"
            :class="index % 2 === 0 ? 'left' : 'right'" data-aos="fade-up">
            <div class="dot"></div>
            <div class="card" :class="`type-${item.type}`" @click="handleCardClick(item)">
              <div v-if="item.img" class="card-img-container" @click.stop="openLightbox(item.img)">
                <img :src="item.img" class="card-img" :alt="item.title">
              </div>

              <div class="card-body">
                <span class="year-tag">{{ item.year }}</span>
                <h3>{{ item.title }}</h3>
                <p>{{ item.content }}</p>
                <div v-if="item.dev" class="dev-box">
                  🔍 {{ item.dev }}
                </div>
              </div>
            </div>
          </div>
        </main>

        <footer>
          <p>
            致敬真正的游戏创作者 |
            <span style="color:#ff6a00">4399</span>
            深度调研报告
          </p>
        </footer>
      </div>

      <!-- <aside class="detail-panel" :class="{ show: showDetail }">
        <transition name="detail-change">
          <div v-if="selectedItem" class="detail-content" :key="selectedItem.title">
            <button class="mobile-close-btn" @click="showDetail = false">
              <span class="icon">▼</span>
            </button>

            <div class="detail-header">
              <span class="year-tag">{{ selectedItem.year }}</span>
              <h2>{{ selectedItem.title }}</h2>
            </div>

            <div class="detail-img">
              <img :src="selectedItem.img" :alt="selectedItem.title">
            </div>

            <div class="detail-body">
              <p>{{ selectedItem.detail }}</p>
              <div v-if="selectedItem.dev" class="dev-box">
                🔍 {{ selectedItem.dev }}
              </div>
            </div>

            <button class="close-btn" @click="showDetail = false">×</button>
          </div>
        </transition>
      </aside> -->

    </div>
    <teleport to="body">
      <div v-if="showIntroModal" class="intro-modal-mask">
        <div class="intro-modal-content">
          <div class="intro-modal-header">
            <h3>{{ introData.title }}</h3>
            <button class="intro-modal-close" @click="closeIntroModal">×</button>
          </div>

          <div class="intro-modal-body" v-html="introData.detail">


          </div>

          <div class="intro-modal-footer">
            <button class="intro-modal-close-btn" @click="closeIntroModal">
              关闭
            </button>
          </div>
        </div>
      </div>
    </teleport>
  </div>


  <div v-if="showLightbox" class="lightbox" @click="closeLightbox">
    <img :src="currentImage" alt="大图预览" @click.stop>
  </div>

</template>

<style>
/* 全局重置 */
body {
  margin: 0;
  padding: 0;
  font-family: 'Noto Sans SC', sans-serif;
  background-color: #2b3a42;
  /* 为了防止 canvas 加载前闪白 */
  color: #333;
  overflow-x: hidden;
  overflow-y: scroll;
}

/* 引言弹窗样式 */
.intro-modal-mask {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background: rgba(0, 0, 0, 0.7);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 9999;
  padding: 20px;
  backdrop-filter: blur(5px);
}

.intro-modal-content {
  background: #fff;
  width: 100%;
  max-width: 800px;
  max-height: 80vh;
  border-radius: 8px;
  overflow: hidden;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.3);
  display: flex;
  flex-direction: column;
}

.intro-modal-header {
  padding: 16px 20px;
  background: #f5f5f5;
  border-bottom: 1px solid #eee;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.intro-modal-header h3 {
  margin: 0;
  font-size: 18px;
  color: #333;
}

.intro-modal-close {
  background: transparent;
  border: none;
  font-size: 24px;
  cursor: pointer;
  color: #666;
  width: 30px;
  height: 30px;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: color 0.2s;
}

.intro-modal-close:hover {
  color: #ff4444;
}

.intro-modal-body {
  padding: 20px;
  overflow-y: auto;
  line-height: 1.6;
  color: #333;
  white-space: pre-line;
}

.intro-modal-body .intro-link {
  color: #1677ff;
  text-decoration: none;
  margin: 0 2px;
}

.intro-modal-body .intro-link:hover {
  text-decoration: underline;
}

.intro-modal-footer {
  padding: 16px 20px;
  border-top: 1px solid #eee;
  display: flex;
  justify-content: flex-end;
  background: #fff;
}

.intro-modal-close-btn {
  padding: 8px 16px;
  background: #1677ff;
  color: #fff;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  transition: background 0.2s;
}

.intro-modal-close-btn:hover {
  background: #0958d9;
}
</style>

<style scoped>
/* 变量定义 */
.app-container {
  --primary: #ff6a00;
  --red: #e74c3c;
  --blue: #3498db;
  --yellow: #f1c40f;
  --card-bg: rgba(255, 255, 255, 0.95);
  /* width: 50%; */
}

/* 核心背景样式 + Canvas */
.app-bg {
  min-height: 100vh;
  width: 100%;
  position: relative;
  background: linear-gradient(-45deg,
      #141e30,
      #243b55,
      #283048);
  background-size: 400% 400%;
  animation: gradientBG 16s ease infinite;
}

/* Canvas 样式 */
.background-canvas {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 0;
  /* 放在底层 */
  pointer-events: none;
  /* 鼠标穿透 */
}

@keyframes gradientBG {
  0% {
    background-position: 0% 50%;
  }

  50% {
    background-position: 100% 50%;
  }

  100% {
    background-position: 0% 50%;
  }
}

/* 主内容区布局 */
.main-content {
  position: relative;
  z-index: 1;
  display: flex;
  width:100vw;
  justify-content: center;
  margin: 0 auto;
}

/* 头部样式 */
.hero {
  width: 100%;
  background: linear-gradient(135deg, rgba(45, 52, 54, 0.8) 0%, rgba(0, 0, 0, 0.9) 100%);
  color: white;
  padding: 80px 20px;
  text-align: center;
  position: relative;
  overflow: hidden;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.3);
  border-radius: 0 0 20px 20px;
  backdrop-filter: blur(10px);
}

.hero h1 {
  font-size: 3.5rem;
  margin: 0;
  letter-spacing: 2px;
  text-shadow: 0 0 10px rgba(255, 106, 0, 0.5);
}

.subtitle {
  font-size: 1.2rem;
  opacity: 0.8;
  margin-top: 10px;
}

/* 筛选按钮 */
.filter-box {
  margin-top: 30px;
  display: flex;
  justify-content: center;
  gap: 15px;
  flex-wrap: wrap;
}

.filter-btn {
  background: rgba(255, 255, 255, 0.1);
  border: 1px solid rgba(255, 255, 255, 0.2);
  color: white;
  padding: 8px 24px;
  border-radius: 50px;
  cursor: pointer;
  backdrop-filter: blur(5px);
  transition: 0.3s;
}

.filter-btn:hover,
.filter-btn.active {
  background: var(--primary);
  border-color: var(--primary);
  transform: translateY(-2px);
}

/* 时间轴容器 */
.timeline-container {
  max-width: none;
  margin: 50px auto;
  position: relative;
}

.timeline-container::after {
  content: '';
  position: absolute;
  width: 6px;
  background: rgba(224, 224, 224, 0.5);
  /* 稍微透明适应深色背景 */
  top: 0;
  bottom: 0;
  left: 50%;
  margin-left: -3px;
  border-radius: 3px;
}

/* 卡片布局 */
.card-wrapper {
  width: 49.5%;
  padding: 10px 50px;
  position: relative;
  box-sizing: border-box;
}

.left {
  left: 0;
  text-align: right;
}

.right {
  left: 50.5%;
  text-align: left;
}

/* 详情面板样式 */
.detail-panel {
  width: 48%;
  padding: 50px;
  background-color: var(--card-bg);
  border-left: 1px solid rgba(255, 215, 0, 0.3);
  box-shadow: -5px 0 25px rgba(0, 0, 0, 0.2);
  overflow-y: auto;
  position: fixed;
  right: 0;
  top: 0;
  height: 100vh;
  opacity: 0;
  transform: translateX(20px);
  transition: all 0.5s cubic-bezier(0.4, 0, 0.2, 1);
  pointer-events: none;
  box-sizing: border-box;
  z-index: 1001;
  /* 确保层级极高，能够接收鼠标滚轮事件 */
  overflow-y: auto;
  /* 确保超长内容出现滚动条 */
  overscroll-behavior: contain;
  /* 防止滚动链效应 */
}

.detail-panel.show {
  opacity: 1;
  transform: translateX(0);
  pointer-events: auto;
}

.detail-content {
  max-width: 800px;
  margin: 0 auto;
  position: relative;
}

.detail-header .year-tag {
  background: #333;
  color: #fff;
  padding: 6px 16px;
  border-radius: 4px;
  font-weight: bold;
  font-size: 1rem;
  display: inline-block;
}

/* --- 详情页标题：亮金色 --- */
.detail-header h2 {
  font-size: 2rem;
  margin: 15px 0 30px;
  /* 修改颜色为亮金 */
  color: #ffd700;
  /* 加上一点文字阴影，保证在任何背景下都清晰可见 */
  text-shadow: 0 2px 4px rgba(0, 0, 0, 0.8);
}

.detail-img {
  width: 100%;
  border-radius: 12px;
  overflow: hidden;
  margin-bottom: 30px;
  box-shadow: 0 5px 15px rgba(0, 0, 0, 0.1);
}

.detail-img img {
  width: 100%;
  height: auto;
  object-fit: cover;
  transition: transform 0.3s ease;
}

.detail-img img:hover {
  transform: scale(1.02);
}

/* --- 详情页正文：浅金色/米白 --- */
.detail-body p {
  font-size: 1.1rem;
  line-height: 1.8;
  margin-bottom: 25px;
  white-space: pre-line;
  /* 修改颜色为浅金色 (阅读长文比纯黄更舒服) */
  color: #fff8e1;
  /* 稍微加点阴影增加对比度 */
  text-shadow: 0 1px 2px rgba(0, 0, 0, 0.6);
  /* 稍微加粗一点点，防止字体太细看不清 */
  font-weight: 500;
}

.detail-body .dev-box {
  background: #fff8e1;
  border-left: 4px solid #ffc107;
  margin-top: 30px;
  padding: 15px;
  font-size: 1rem;
  color: #8d6e63;
  border-radius: 0 8px 8px 0;
}

/* 关闭按钮 (桌面端) */
.close-btn {
  position: fixed;
  top: 20px;
  right: 20px;
  width: 40px;
  height: 40px;
  border-radius: 50%;
  background: rgba(0, 0, 0, 0.7);
  color: white;
  border: none;
  font-size: 1.5rem;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.3s ease;
  z-index: 100;
}

.close-btn:hover {
  background: var(--primary);
  transform: rotate(90deg);
}

/* 移动端关闭按钮 */
.mobile-close-btn {
  display: none;
}

/* 卡片样式 */
.card {
  background: var(--card-bg);
  border-radius: 16px;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.2);
  overflow: hidden;
  transition: all 0.3s ease;
  border-top: 5px solid #ccc;
  text-align: left;
  cursor: pointer;
}

.card:hover {
  transform: translateY(-8px);
  box-shadow: 0 15px 40px rgba(0, 0, 0, 0.3);
}

/* 类型颜色条 */
.card.type-game {
  border-top-color: var(--primary);
}

.card.type-true {
  border-top-color: var(--red);
}

.card.type-flash {
  border-top-color: var(--blue);
}

.card.type-milestone {
  border-top-color: var(--yellow);
}

.card-img-container {
  width: 100%;
  height: 180px;
  overflow: hidden;
  cursor: zoom-in;
  background: #f0f0f0;
  border-bottom: 1px solid #eee;
}

.card-img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  transition: 0.5s;
}

.card:hover .card-img {
  transform: scale(1.1);
}

.card-body {
  padding: 25px;
}

.year-tag {
  background: #333;
  color: #fff;
  padding: 4px 12px;
  border-radius: 4px;
  font-weight: bold;
  font-size: 0.85rem;
  display: inline-block;
  margin-bottom: 10px;
}

.card h3 {
  margin: 10px 0;
  font-size: 1.4rem;
  color: #2c3e50;
}

.card p {
  color: #666;
  line-height: 1.6;
  font-size: 0.95rem;
  margin-bottom: 15px;
}

.dev-box {
  background: #fff8e1;
  border-left: 4px solid #ffc107;
  padding: 12px;
  border-radius: 0 8px 8px 0;
  font-size: 0.9rem;
  color: #8d6e63;
}

/* 圆点 */
.dot {
  width: 24px;
  height: 24px;
  background: white;
  border: 5px solid var(--primary);
  border-radius: 50%;
  position: absolute;
  top: 30px;
  z-index: 2;
  box-shadow: 0 0 0 4px rgba(255, 106, 0, 0.2);
}

.left .dot {
  right: -17px;
}

.right .dot {
  left: -17px;
}

/* 灯箱 */
.lightbox {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.85);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 2001;
  backdrop-filter: blur(5px);
}

.lightbox img {
  max-width: 90%;
  max-height: 90%;
  border-radius: 8px;
  box-shadow: 0 0 20px rgba(0, 0, 0, 0.5);
  animation: popIn 0.3s ease;
}

@keyframes popIn {
  from {
    transform: scale(0.8);
    opacity: 0;
  }

  to {
    transform: scale(1);
    opacity: 1;
  }
}

footer {
  text-align: center;
  padding: 40px;
  color: #ccc;
  /* 调整为浅色以适应深背景 */
  font-size: 0.9rem;
}

/* 响应式适配 */
@media (max-width: 1024px) {
  .main-content {
    flex-direction: column;
  }

  .app-container {
    width: 100%;
  }

  /* 移动端详情页改为全屏覆盖 */
  .detail-panel {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100vh;
    z-index: 2000;
    margin: 0;
    border: none;
    background: #fff;
    transform: translateY(100%);
    transition: transform 0.3s cubic-bezier(0.4, 0, 0.2, 1);
    opacity: 1;
  }

  .detail-panel.show {
    transform: translateY(0);
    opacity: 1;
  }

  .detail-content {
    padding-bottom: 80px;
    padding-top: 60px;
  }

  .close-btn {
    display: none;
  }

  .mobile-close-btn {
    display: flex;
    justify-content: center;
    align-items: center;
    position: absolute;
    top: 10px;
    left: 50%;
    transform: translateX(-50%);
    width: 100%;
    height: 40px;
    background: transparent;
    border: none;
    font-size: 20px;
    color: #999;
    cursor: pointer;
  }

  .mobile-close-btn .icon {
    animation: bounce 2s infinite;
  }

  @keyframes bounce {

    0%,
    20%,
    50%,
    80%,
    100% {
      transform: translateY(0);
    }

    40% {
      transform: translateY(5px);
    }

    60% {
      transform: translateY(3px);
    }
  }

  /* 时间轴调整 */
  .timeline-container::after {
    left: 30px;
  }

  .card-wrapper {
    width: 100%;
    padding-left: 70px;
    padding-right: 20px;
    text-align: left;
  }

  .card-wrapper.left,
  .card-wrapper.right {
    left: 0;
  }

  .left .dot,
  .right .dot {
    left: 13px;
    right: auto;
  }
}

/* 美化滚动条 */
.detail-panel::-webkit-scrollbar,
.intro-modal-body::-webkit-scrollbar {
  width: 6px;
}

.detail-panel::-webkit-scrollbar-thumb,
.intro-modal-body::-webkit-scrollbar-thumb {
  background: #ccc;
  border-radius: 3px;
}

.detail-panel::-webkit-scrollbar-track,
.intro-modal-body::-webkit-scrollbar-track {
  background: transparent;
}
</style>