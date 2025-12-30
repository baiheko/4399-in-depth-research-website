<script setup>
import { ref, computed, onMounted, nextTick } from 'vue';
import AOS from 'aos';
import 'aos/dist/aos.css';

// --- 状态管理 ---
const currentFilter = ref('all');
const showLightbox = ref(false);
const currentImage = ref('');

// --- 核心数据 ---
const timelineData = [
  {
    year: "2002",
    type: "milestone",
    title: "草根起源：Hao123 的影子",
    content: "李兴平（Hao123创始人）创建了 4399。初衷是为了解决网吧顾客记不住网址的问题，它迅速成为了国内最大的“游戏杂货铺”。",
    // 这里的路径对应 public/image/hao123.png
    img: "/image/hao123.png" 
  },
  {
    year: "2003-2008",
    type: "game",
    title: "《黄金矿工》的真相",
    content: "4399 上的绝对经典。但这是美国插画家 Dan Glover 制作的游戏。4399 未经授权破译并发布，原作者未获分文。",
    dev: "真正开发者：Dan Glover (GameRival)",
    img: "/image/gold.jpg"
  },
  {
    year: "2008",
    type: "business",
    title: "资本注入与摩尔庄园",
    content: "蔡文胜注资，引入骆海坚。同年，4399 与淘米网络合作，引入《摩尔庄园》，正式开启页游联运时代。",
    img: "/image/moer.jpg"
  },
  {
    year: "2009",
    type: "game",
    title: "《植物大战僵尸》汉化版",
    content: "PopCap 的神作风靡全球。4399 上充斥着扒掉 Logo 的版本，让许多玩家误以为这是 4399 的原创游戏。",
    dev: "真正开发者：PopCap Games (美国)",
    img: "/image/plants.jpg"
  },
  {
    year: "2010",
    type: "game",
    title: "被“整合”的 Nitrome",
    content: "英国像素工作室 Nitrome 的《坏蛋冰淇淋》、《双刃战士》等精品被批量搬运。这是一家极具创意的独立工作室。",
    dev: "真正开发者：Nitrome (英国)",
    img: "/image/doubleedged.jpg"
  },
  {
    year: "2016",
    type: "controversy",
    title: "诉讼缠身：DNF 商标案",
    content: "4399 因侵权《地下城与勇士》被判赔腾讯 500 万。游戏商标纠纷中赔偿额最高的案件之一。",
    img: "/image/dnf.jpg"
  },
  {
    year: "2017",
    type: "controversy",
    title: "守望先锋 vs 英雄枪战",
    content: "暴雪和网易联合起诉 4399 的《英雄枪战》抄袭《守望先锋》，4399 败诉并赔偿 397 万元。",
    img: "/image/baoxue.png"
  },
  {
    year: "2020",
    type: "milestone",
    title: "Flash 时代的终结",
    content: "Adobe 正式停止 Flash 支持。依赖 Flash 起家的 4399 遭受毁灭性打击，不得不全面转向手游。",
    img: "/image/flash.webp"
  },
  {
    year: "2023",
    type: "business",
    title: "墙内开花墙外香",
    content: "国内日活跌破百万，但凭借《姑勇者传说》等手游在海外横扫榜单，成功转型出海。",
    img: "/image/guyongzhe.webp"
  }
];

// --- 计算属性：筛选逻辑 ---
const filteredData = computed(() => {
  if (currentFilter.value === 'all') {
    return timelineData;
  }
  return timelineData.filter(item => item.type === currentFilter.value);
});

// --- 方法 ---
const setFilter = (type) => {
  currentFilter.value = type;
  // 切换筛选后刷新动画
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

// --- 生命周期 ---
onMounted(() => {
  AOS.init({
    duration: 800,
    once: false, // 允许滚动回看时重复触发动画
  });
});
</script>

<template>
  <div class="app-container">
    <header class="hero">
      <div class="hero-content" data-aos="fade-down">
        <h1>4399 编年史</h1>
        <p class="subtitle">童年乐园 · 盗版帝国 · 出海巨头</p>
        
        <div class="filter-box">
          <button 
            v-for="btn in [
              { label: '全部档案', value: 'all' },
              { label: '发展历程', value: 'milestone' },
              { label: '游戏真相', value: 'game' },
              { label: '版权争议', value: 'controversy' },
              { label: '商业转型', value: 'business' }
            ]"
            :key="btn.value"
            class="filter-btn"
            :class="{ active: currentFilter === btn.value }"
            @click="setFilter(btn.value)"
          >
            {{ btn.label }}
          </button>
        </div>
      </div>
      <div class="bg-grid"></div>
    </header>

    <main class="timeline-container">
      <div 
        v-for="(item, index) in filteredData" 
        :key="index"
        class="card-wrapper"
        :class="index % 2 === 0 ? 'left' : 'right'"
        data-aos="fade-up"
      >
        <div class="dot"></div>
        <div class="card" :class="`type-${item.type}`">
          <div 
            v-if="item.img" 
            class="card-img-container" 
            @click="openLightbox(item.img)"
          >
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

    <div v-if="showLightbox" class="lightbox" @click="closeLightbox">
      <img :src="currentImage" alt="大图预览" @click.stop>
    </div>

    <footer>
      <p>致敬真正的游戏创作者 | <span style="color:#ff6a00">4399</span> 深度调研报告</p>
    </footer>
  </div>
</template>

<style>
/* 全局重置 */
body {
  margin: 0;
  padding: 0;
  font-family: 'Noto Sans SC', sans-serif;
  background-color: #eef2f5;
  background-image: radial-gradient(#dce1e6 1px, transparent 1px);
  background-size: 20px 20px;
  color: #333;
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
}

/* 头部样式 */
.hero {
  width: 50vw;
  background: linear-gradient(135deg, #2d3436 0%, #000000 100%);
  color: white;
  padding: 80px 20px;
  text-align: center;
  position: relative;
  overflow: hidden;
  box-shadow: 0 4px 20px rgba(0,0,0,0.3);
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
  background: rgba(255,255,255,0.1);
  border: 1px solid rgba(255,255,255,0.2);
  color: white;
  padding: 8px 24px;
  border-radius: 50px;
  cursor: pointer;
  backdrop-filter: blur(5px);
  transition: 0.3s;
}

.filter-btn:hover, .filter-btn.active {
  background: var(--primary);
  border-color: var(--primary);
  transform: translateY(-2px);
}

/* 时间轴容器 */
.timeline-container {
  width: 50vw;
  max-width: 1000px;
  margin: 50px 0;
  position: relative;
}

.timeline-container::after {
  content: '';
  position: absolute;
  width: 6px;
  background: #e0e0e0;
  top: 0; bottom: 0; left: 50%;
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

.left { left: 0; text-align: right; }
.right { left: 50.5%; text-align: left; }

/* 响应式适配 */
@media (max-width: 768px) {
  .timeline-container::after { left: 30px; }
  .card-wrapper { width: 100%; padding-left: 70px; padding-right: 20px; text-align: left; }
  .card-wrapper.left, .card-wrapper.right { left: 0; }
  .left .dot, .right .dot { left: 13px; right: auto; }
}

/* 卡片样式 */
.card {
  background: var(--card-bg);
  border-radius: 16px;
  box-shadow: 0 10px 30px rgba(0,0,0,0.08);
  overflow: hidden;
  transition: all 0.3s ease;
  border-top: 5px solid #ccc;
  text-align: left; /* 强制内容左对齐 */
}

.card:hover {
  transform: translateY(-8px);
  box-shadow: 0 15px 40px rgba(0,0,0,0.15);
}

/* 类型颜色条 */
.card.type-game { border-top-color: var(--primary); }
.card.type-controversy { border-top-color: var(--red); }
.card.type-business { border-top-color: var(--blue); }
.card.type-milestone { border-top-color: var(--yellow); }

/* 图片 */
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

.card:hover .card-img { transform: scale(1.1); }

/* 内容区 */
.card-body { padding: 25px; }

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

/* 开发者框 */
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

.left .dot { right: -17px; }
.right .dot { left: -17px; }

/* 灯箱 */
.lightbox {
  position: fixed;
  top: 0; left: 0;
  width: 100%; height: 100%;
  background: rgba(0,0,0,0.85);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
  backdrop-filter: blur(5px);
}

.lightbox img {
  max-width: 90%;
  max-height: 90%;
  border-radius: 8px;
  box-shadow: 0 0 20px rgba(0,0,0,0.5);
  animation: popIn 0.3s ease;
}

@keyframes popIn {
  from { transform: scale(0.8); opacity: 0; }
  to { transform: scale(1); opacity: 1; }
}

footer {
  text-align: center;
  padding: 40px;
  color: #999;
  font-size: 0.9rem;
}
</style>