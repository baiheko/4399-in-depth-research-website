<script setup>
import { ref, computed, onMounted, nextTick } from 'vue';
import AOS from 'aos';
import 'aos/dist/aos.css';

// --- 状态管理 ---
const currentFilter = ref('all');
const showLightbox = ref(false);
const currentImage = ref('');
// 添加选中项状态管理
const selectedItem = ref(null);
const showDetail = ref(false);

// --- 核心数据 ---
const timelineData = [
  {
    year: "2002",
    type: "milestone",
    title: "草根起源：Hao123 的影子",
    content: "李兴平（Hao123创始人）创建了 4399。初衷是为了解决网吧顾客记不住网址的问题，它迅速成为了国内最大的“游戏杂货铺”。",
    // 这里的路径对应 public/image/hao123.png
    img: "/image/hao123.png" ,
    // 新增详细信息
    detail: "2002年，互联网在中国仍处于发展初期，大多数网吧用户对于复杂的网址记忆困难。李兴平凭借其对网民需求的敏锐洞察，创建了4399游戏平台。最初只是一个简单的游戏导航网站，集合了当时互联网上各种免费小游戏的链接，因其便捷性迅速在网吧用户中传播开来。这个时期的4399界面简陋，但精准抓住了用户痛点，为其后来的发展奠定了基础。"
  },
  {
    year: "2003-2008",
    type: "game",
    title: "《黄金矿工》的真相",
    content: "4399 上的绝对经典。但这是美国插画家 Dan Glover 制作的游戏。4399 未经授权破译并发布，原作者未获分文。",
    dev: "真正开发者：Dan Glover (GameRival)",
    img: "/image/gold.jpg",
    // 新增详细信息
    detail: "2002年，互联网在中国仍处于发展初期，大多数网吧用户对于复杂的网址记忆困难。李兴平凭借其对网民需求的敏锐洞察，创建了4399游戏平台。最初只是一个简单的游戏导航网站，集合了当时互联网上各种免费小游戏的链接，因其便捷性迅速在网吧用户中传播开来。这个时期的4399界面简陋，但精准抓住了用户痛点，为其后来的发展奠定了基础。"
  
  },
  {
    year: "2008",
    type: "business",
    title: "资本注入与摩尔庄园",
    content: "蔡文胜注资，引入骆海坚。同年，4399 与淘米网络合作，引入《摩尔庄园》，正式开启页游联运时代。",
    img: "/image/moer.jpg",
    // 新增详细信息
    detail: "2002年，互联网在中国仍处于发展初期，大多数网吧用户对于复杂的网址记忆困难。李兴平凭借其对网民需求的敏锐洞察，创建了4399游戏平台。最初只是一个简单的游戏导航网站，集合了当时互联网上各种免费小游戏的链接，因其便捷性迅速在网吧用户中传播开来。这个时期的4399界面简陋，但精准抓住了用户痛点，为其后来的发展奠定了基础。"
  
  },
  {
    year: "2009",
    type: "game",
    title: "《植物大战僵尸》汉化版",
    content: "PopCap 的神作风靡全球。4399 上充斥着扒掉 Logo 的版本，让许多玩家误以为这是 4399 的原创游戏。",
    dev: "真正开发者：PopCap Games (美国)",
    img: "/image/plants.jpg",
    // 新增详细信息
    detail: "2002年，互联网在中国仍处于发展初期，大多数网吧用户对于复杂的网址记忆困难。李兴平凭借其对网民需求的敏锐洞察，创建了4399游戏平台。最初只是一个简单的游戏导航网站，集合了当时互联网上各种免费小游戏的链接，因其便捷性迅速在网吧用户中传播开来。这个时期的4399界面简陋，但精准抓住了用户痛点，为其后来的发展奠定了基础。"
  
  },
  {
    year: "2010",
    type: "game",
    title: "被“整合”的 Nitrome",
    content: "英国像素工作室 Nitrome 的《坏蛋冰淇淋》、《双刃战士》等精品被批量搬运。这是一家极具创意的独立工作室。",
    dev: "真正开发者：Nitrome (英国)",
    img: "/image/doubleedged.jpg",
    // 新增详细信息
    detail: "2002年，互联网在中国仍处于发展初期，大多数网吧用户对于复杂的网址记忆困难。李兴平凭借其对网民需求的敏锐洞察，创建了4399游戏平台。最初只是一个简单的游戏导航网站，集合了当时互联网上各种免费小游戏的链接，因其便捷性迅速在网吧用户中传播开来。这个时期的4399界面简陋，但精准抓住了用户痛点，为其后来的发展奠定了基础。"
  
  },
  {
    year: "2016",
    type: "controversy",
    title: "诉讼缠身：DNF 商标案",
    content: "4399 因侵权《地下城与勇士》被判赔腾讯 500 万。游戏商标纠纷中赔偿额最高的案件之一。",
    img: "/image/dnf.jpg",
    // 新增详细信息
    detail: "2002年，互联网在中国仍处于发展初期，大多数网吧用户对于复杂的网址记忆困难。李兴平凭借其对网民需求的敏锐洞察，创建了4399游戏平台。最初只是一个简单的游戏导航网站，集合了当时互联网上各种免费小游戏的链接，因其便捷性迅速在网吧用户中传播开来。这个时期的4399界面简陋，但精准抓住了用户痛点，为其后来的发展奠定了基础。"
  
  },
  {
    year: "2017",
    type: "controversy",
    title: "守望先锋 vs 英雄枪战",
    content: "暴雪和网易联合起诉 4399 的《英雄枪战》抄袭《守望先锋》，4399 败诉并赔偿 397 万元。",
    img: "/image/baoxue.png",
    // 新增详细信息
    detail: "2002年，互联网在中国仍处于发展初期，大多数网吧用户对于复杂的网址记忆困难。李兴平凭借其对网民需求的敏锐洞察，创建了4399游戏平台。最初只是一个简单的游戏导航网站，集合了当时互联网上各种免费小游戏的链接，因其便捷性迅速在网吧用户中传播开来。这个时期的4399界面简陋，但精准抓住了用户痛点，为其后来的发展奠定了基础。"
  
  },
  {
    year: "2020",
    type: "milestone",
    title: "Flash 时代的终结",
    content: "Adobe 正式停止 Flash 支持。依赖 Flash 起家的 4399 遭受毁灭性打击，不得不全面转向手游。",
    img: "/image/flash.webp",
    // 新增详细信息
    detail: "2002年，互联网在中国仍处于发展初期，大多数网吧用户对于复杂的网址记忆困难。李兴平凭借其对网民需求的敏锐洞察，创建了4399游戏平台。最初只是一个简单的游戏导航网站，集合了当时互联网上各种免费小游戏的链接，因其便捷性迅速在网吧用户中传播开来。这个时期的4399界面简陋，但精准抓住了用户痛点，为其后来的发展奠定了基础。"
  
  },
  {
    year: "2023",
    type: "business",
    title: "墙内开花墙外香",
    content: "国内日活跌破百万，但凭借《姑勇者传说》等手游在海外横扫榜单，成功转型出海。",
    img: "/image/guyongzhe.webp",
    // 新增详细信息
    detail: "2002年，互联网在中国仍处于发展初期，大多数网吧用户对于复杂的网址记忆困难。李兴平凭借其对网民需求的敏锐洞察，创建了4399游戏平台。最初只是一个简单的游戏导航网站，集合了当时互联网上各种免费小游戏的链接，因其便捷性迅速在网吧用户中传播开来。这个时期的4399界面简陋，但精准抓住了用户痛点，为其后来的发展奠定了基础。"
  
  }
];

// 点击卡片处理函数
const handleCardClick = (item) => {
  selectedItem.value = item;
  // 用于触发动画的状态切换
  showDetail.value = false;
  // 等待DOM更新后显示详情，触发过渡动画
  nextTick(() => {
    showDetail.value = true;
  });
};

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
  <div class="main-content">
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

      
        <!-- 时间轴容器 -->
        <main class="timeline-container">
          <div 
            v-for="(item, index) in filteredData" 
            :key="index"
            class="card-wrapper"
            :class="index % 2 === 0 ? 'left' : 'right'"
            data-aos="fade-up"
          >
            <div class="dot"></div>
            <div class="card" :class="`type-${item.type}`"@click="handleCardClick(item)">
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
    <!-- 右侧详情区域 -->
        <aside class="detail-panel" :class="{ 'show': showDetail }">
          <div v-if="selectedItem" class="detail-content">
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
          
          <div v-else class="empty-state">
            <p>点击左侧卡片查看详细信息</p>
          </div>
        </aside>
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
/* 主内容区布局 */
.main-content {
  display: flex;
  width: 97vw;
  margin: 0 auto;
}
/* 变量定义 */
.app-container {
  --primary: #ff6a00;
  --red: #e74c3c;
  --blue: #3498db;
  --yellow: #f1c40f;
  --card-bg: rgba(255, 255, 255, 0.95);
  width: 50%;
}

/* 头部样式 */
.hero {
  width: 100%;
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
  max-width: none;
  margin: 50px auto;
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

/* 详情面板样式 */
.detail-panel {
  width: 48%;
  padding: 50px;
  background-color: var(--card-bg);
  border-left: 1px solid #eee;
  box-shadow: -5px 0 25px rgba(0,0,0,0.05);
  overflow-y: auto;
  min-height: calc(100vh - 220px); /* 减去头部和底部高度 */
  position: fixed;
  right: 0;
  top: 0;
  height: 100vh; /* 占满整个视口高度 */
  opacity: 0;
  transform: translateX(20px);
  transition: all 0.5s cubic-bezier(0.4, 0, 0.2, 1);
  pointer-events: none; /* 隐藏时不响应事件 */
  margin: 0 1vw;
}

/* 显示详情面板的动画状态 */
.detail-panel.show {
  opacity: 1;
  transform: translateX(0);
  pointer-events: auto;
}

/* 详情内容样式 */
.detail-content {
  max-width: 800px;
  margin: 0 auto;
}

.detail-header .year-tag {
  font-size: 1rem;
  padding: 6px 16px;
}

.detail-header h2 {
  font-size: 2rem;
  margin: 15px 0 30px;
  color: #2c3e50;
}

.detail-img {
  width: 100%;
  border-radius: 12px;
  overflow: hidden;
  margin-bottom: 30px;
  box-shadow: 0 5px 15px rgba(0,0,0,0.1);
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

.detail-body p {
  font-size: 1.1rem;
  line-height: 1.8;
  color: #444;
  margin-bottom: 25px;
}

.detail-body .dev-box {
  margin-top: 30px;
  padding: 15px;
  font-size: 1rem;
}

/* 关闭按钮 */
.close-btn {
  position: fixed;
  top: 20px;
  right: 20px;
  width: 40px;
  height: 40px;
  border-radius: 50%;
  background: rgba(0,0,0,0.7);
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

/* 空状态样式 */
.empty-state {
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  color: #999;
  font-size: 1.2rem;
  text-align: center;
  padding: 20px;
}



/* 响应式适配 */
@media (max-width: 1024px) {
  .main-content {
    flex-direction: column;
  }
  
  .timeline-container, .detail-panel {
    width: 100%;
  }
  
  .detail-panel {
    min-height: auto;
    border-left: none;
    border-top: 1px solid #eee;
  }
  
  .detail-header h2 {
    font-size: 1.5rem;
  }
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