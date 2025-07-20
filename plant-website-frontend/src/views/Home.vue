<template>
  <div class="home-container">
    <!-- 顶部通知栏 -->
    <div class="announcement-bar">
      <div class="container">
        <span class="announcement-text">{{ notice }}</span>
      </div>
    </div>
    
    <!-- 主头部 -->
    <header class="main-header">
      <div class="container header-content">
        <div class="logo-area">
          <h1 class="logo">植多多</h1>
          <p class="slogan">探索植物世界 · 守护绿色家园</p>
        </div>
        
        <div class="search-area">
          <div class="search-box">
            <input 
              v-model="search" 
              type="text" 
              placeholder="搜索植物名称/类别/关键词..." 
              @keyup.enter="onSearch"
            >
            <button class="search-btn" @click="onSearch">
              <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                <circle cx="11" cy="11" r="8"></circle>
                <line x1="21" y1="21" x2="16.65" y2="16.65"></line>
              </svg>
            </button>
          </div>
        </div>
      </div>
    </header>
    
    <!-- 主导航 -->
    <nav class="main-nav">
      <div class="container no-padding">
        <ul class="nav-list">
          <li 
            v-for="(cat, index) in categories" 
            :key="index" 
            @mouseenter="showSubMenu(index)"
            @mouseleave="hideSubMenu(index)"
          >
            <a href="#" @click.prevent="onCategory(cat)" :class="{ active: activeCategory === index }">
              {{ cat }}
            </a>
            <!-- 二级菜单 -->
            <transition name="fade">
              <ul v-if="activeSubMenu === index" class="sub-menu">
                <li v-for="(subItem, subIndex) in subMenus[index]" :key="subIndex">
                  <a href="#">{{ subItem }}</a>
                </li>
              </ul>
            </transition>
          </li>
        </ul>
      </div>
    </nav>
    
    <!-- 主要内容区 -->
    <main class="main-content">
      <div class="container">
        <!-- 主轮播区 -->
        <section class="hero-section">
          <div class="hero-carousel" 
               @mouseenter="hover = true"
               @mouseleave="hover = false">
            <swiper
              :modules="[Pagination, Autoplay]"
              :slides-per-view="1"
              :loop="true"
              :autoplay="{ delay: 5000, disableOnInteraction: false }"
              :pagination="{ clickable: true }"
              @slideChange="onSlideChange"
              @swiper="onSwiper"
            >
              <swiper-slide v-for="(item, index) in carouselItems" :key="index">
                <div class="carousel-item">
                  <img :src="item.img" :alt="item.title" class="carousel-image">
                  <div class="carousel-content">
                    <h2 class="carousel-title">{{ item.title }}</h2>
                  </div>
                </div>
              </swiper-slide>
            </swiper>
            
            <!-- 毛玻璃区域 -->
            <div class="swiper-glass" :class="{ 'swiper-glass-hover': hover }">
              <div class="glass-thumbs">
                <div
                  v-for="(item, idx) in carouselItems"
                  :key="idx"
                  class="glass-thumb"
                  :class="{ active: idx === activeIndex }"
                  @mouseenter="handleThumbHover(idx)"
                >
                  <img :src="item.img" />
                  <div class="thumb-title">{{ item.title }}</div>
                </div>
              </div>
            </div>
          </div>
        </section>
        
        <!-- 植物分类展示区 - 四行布局 -->
        <section class="plant-categories">
          <div v-for="(row, index) in plantRows" :key="index" class="category-row">
            <h3 class="category-title">
              <span>{{ row.title }}</span>
              <a href="#" class="view-more">查看更多 &raquo;</a>
            </h3>
            <div class="plant-scroller">
              <div 
                class="plant-scroll-container"
                @mouseenter="pauseScroll(index)"
                @mouseleave="resumeScroll(index)"
              >
                <div class="plant-list">
                  <a 
                    v-for="(img, imgIndex) in row.images" 
                    :key="imgIndex" 
                    href="#" 
                    class="plant-card"
                  >
                    <div class="plant-image">
                      <img :src="img" :alt="row.title + imgIndex">
                    </div>
                    <div class="plant-info">
                      <h4>{{ getPlantName(row.title, imgIndex) }}</h4>
                      <p>{{ getLatinName(row.title, imgIndex) }}</p>
                    </div>
                  </a>
                  <!-- 复制一份实现无缝滚动 -->
                  <a 
                    v-for="(img, imgIndex) in row.images" 
                    :key="imgIndex + '-copy'" 
                    href="#" 
                    class="plant-card"
                  >
                    <div class="plant-image">
                      <img :src="img" :alt="row.title + imgIndex">
                    </div>
                    <div class="plant-info">
                      <h4>{{ getPlantName(row.title, imgIndex) }}</h4>
                      <p>{{ getLatinName(row.title, imgIndex) }}</p>
                    </div>
                  </a>
                </div>
              </div>
            </div>
          </div>
        </section>
        
        <!-- 资讯区 -->
        <section class="info-section">
          <div class="video-container">
            <div class="section-header">
              <h3>植物科普视频</h3>
              <a href="#" class="section-more">更多视频 &raquo;</a>
            </div>
            <div class="video-wrapper">
              <video controls :poster="videoPoster">
                <source :src="videoUrl" type="video/mp4">
                您的浏览器不支持 video 标签。
              </video>
              <div class="video-info">
                <h4>神奇的植物世界</h4>
                <p>探索植物如何适应环境并与生态系统互动</p>
              </div>
            </div>
          </div>
          
          <div class="news-container">
            <div class="section-header">
              <h3>植物保护新闻</h3>
              <a href="#" class="section-more">更多新闻 &raquo;</a>
            </div>
            <ul class="news-list">
              <li v-for="(news, index) in newsList" :key="index" class="news-item">
                <a href="#">
                  <span class="news-date">07/{{ 10 + index }}</span>
                  <span class="news-title">{{ news }}</span>
                </a>
              </li>
            </ul>
          </div>
        </section>
      </div>
    </main>
    
    <!-- 页脚 -->
    <footer class="new-footer">
      <div class="footer-wave"></div>
      <div class="footer-content">
        <div class="footer-column">
          <h3 class="footer-title">关于植多多</h3>
          <p class="footer-text">植多多是一个致力于植物科普和生态保护的平台，提供全面的植物知识、保护资讯和科研动态。</p>
        </div>
        <div class="footer-column">
          <h3 class="footer-title">快速链接</h3>
          <ul class="footer-links">
            <li><a href="#" class="footer-link">植物数据库</a></li>
            <li><a href="#" class="footer-link">保护政策</a></li>
            <li><a href="#" class="footer-link">科普活动</a></li>
            <li><a href="#" class="footer-link">科研合作</a></li>
          </ul>
        </div>
        <div class="footer-column">
          <h3 class="footer-title">联系我们</h3>
          <div class="contact-item">
            <svg class="contact-icon" viewBox="0 0 24 24"><path d="M20 4H4c-1.1 0-2 .9-2 2v12c0 1.1.9 2 2 2h16c1.1 0 2-.9 2-2V6c0-1.1-.9-2-2-2zm0 4l-8 5-8-5V6l8 5 8-5v2z"/></svg>
            <span>contact@plantmore.com</span>
          </div>
          <div class="contact-item">
            <svg class="contact-icon" viewBox="0 0 24 24"><path d="M20.01 15.38c-1.23 0-2.42-.2-3.53-.56-.35-.12-.74-.03-1.01.24l-1.57 1.97c-2.83-1.35-5.48-3.9-6.89-6.83l1.95-1.66c.27-.28.35-.67.24-1.02-.37-1.11-.56-2.3-.56-3.53 0-.54-.45-.99-.99-.99H4.19C3.65 3 3 3.24 3 3.99 3 13.28 10.73 21 20.01 21c.71 0 .99-.63.99-1.18v-3.45c0-.54-.45-.99-.99-.99z"/></svg>
            <span>400-123-4567</span>
          </div>
        </div>
      </div>
      <div class="footer-bottom">
        <p>&copy; 2025 植多多 - 植物科普平台 | 数据来源: 国家植物数据库、国际植物保护联盟</p>
      </div>
    </footer>
  </div>
</template>

<script setup>
// 导入Vue相关功能
import { ref, onMounted } from 'vue'
// 导入Swiper组件和模块
import { Swiper, SwiperSlide } from 'swiper/vue'
import { Pagination, Autoplay } from 'swiper/modules'
// 导入Swiper样式
import 'swiper/css'
import 'swiper/css/pagination'

// 数据定义部分

// 顶部公告内容
const notice = ref('最新公告：2025年全国植物普查工作已启动，欢迎各界人士参与植物保护志愿者活动。')
// 搜索框内容
const search = ref('')
// 当前激活的导航项索引
const activeCategory = ref(0)
// 当前显示的二级菜单索引
const activeSubMenu = ref(null)
// 鼠标是否悬停在轮播图上
const hover = ref(false)

// 导航分类数组
const categories = [
  '森林植被', '草原植被', '荒漠植被', '湿地植被', 
  '高山植被', '海岸植被', '海洋植被'
]

// 二级菜单数组
const subMenus = [
  ['热带雨林', '亚热带常绿阔叶林', '温带落叶阔叶林', '寒温带针叶林', '针阔混交林'],
  ['温带草原', '高寒草原', '热带草原'],
  ['温带荒漠', '极旱荒漠', '高寒荒漠'],
  ['草本沼泽', '木本沼泽', '盐沼'],
  ['高山草甸', '高山灌丛', '高山流石滩植被'],
  ['红树林', '沙生植被', '盐生植被'],
  ['海藻植物', '海草植物', '浮游植物']
]

// 轮播图数据
const carouselItems = [
  {
    img: 'https://images.unsplash.com/photo-1605559424843-9e4c228bf1c2?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=1200&q=80',
    title: '热带雨林植物多样性研究',
    link: '#'
  },
  {
    img: 'https://images.unsplash.com/photo-1526397751294-331021109fbd?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=1200&q=80',
    title: '高山植物适应极端环境研究',
    link: '#'
  },
  {
    img: 'https://images.unsplash.com/photo-1490730141103-6cac27aaab94?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=1200&q=80',
    title: '城市绿化植物优选指南',
    link: '#'
  }
]

// 四行植物分类数据
const plantRows = [
  {
    title: '森林植被',
    images: [
      'https://images.unsplash.com/photo-1448375240586-882707db888b?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=300&q=80',
      'https://images.unsplash.com/photo-1476231682828-37e571bc172f?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=300&q=80',
      'https://images.unsplash.com/photo-1425913397330-cf8af2ff40a1?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=300&q=80',
      'https://images.unsplash.com/photo-1444464666168-49d633062867?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=300&q=80'
    ]
  },
  {
    title: '草原植被',
    images: [
      'https://images.unsplash.com/photo-1500382017468-9049fed747ef?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=300&q=80',
      'https://images.unsplash.com/photo-1518173946687-a4c8892bbd9f?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=300&q=80',
      'https://images.unsplash.com/photo-1496614932622-5d5dfe5ad5f4?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=300&q=80',
      'https://images.unsplash.com/photo-1504208434309-cb69f4fe52b0?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=300&q=80'
    ]
  },
  {
    title: '荒漠植被',
    images: [
      'https://images.unsplash.com/photo-1509316785289-025f5b846b35?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=300&q=80',
      'https://images.unsplash.com/photo-1507608869274-d3177c8bb4c7?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=300&q=80',
      'https://images.unsplash.com/photo-1509316785289-025f5b846b35?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=300&q=80',
      'https://images.unsplash.com/photo-1509316785289-025f5b846b35?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=300&q=80'
    ]
  },
  {
    title: '湿地植被',
    images: [
      'https://images.unsplash.com/photo-1470114716159-e389f8712fda?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=300&q=80',
      'https://images.unsplash.com/photo-1470114716159-e389f8712fda?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=300&q=80',
      'https://images.unsplash.com/photo-1470114716159-e389f8712fda?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=300&q=80',
      'https://images.unsplash.com/photo-1470114716159-e389f8712fda?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=300&q=80'
    ]
  }
]

// 视频URL和封面图
const videoUrl = 'https://www.w3schools.com/html/mov_bbb.mp4'
const videoPoster = 'https://images.unsplash.com/photo-1490730141103-6cac27aaab94?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=1200&q=80'
// 新闻列表
const newsList = [
  '《国家植物保护法》修订案通过，加强濒危物种保护',
  '2025年全球植物多样性报告发布，揭示保护现状',
  '中国科学院发现新型抗旱植物基因',
  '城市绿化新标准：优先选用本地植物物种'
]

// 获取植物名称的函数
function getPlantName(category, index) {
  // 根据分类和索引返回对应的植物名称
  const names = {
    '森林植被': ['橡树', '松树', '枫树', '桦树'],
    '草原植被': ['羊草', '针茅', '苜蓿', '蒿草'],
    '荒漠植被': ['仙人掌', '梭梭', '骆驼刺', '沙柳'],
    '湿地植被': ['芦苇', '香蒲', '水葱', '菖蒲']
  }
  return names[category][index] || `植物 ${index + 1}`
}

// 获取植物拉丁名的函数
function getLatinName(category, index) {
  // 根据分类和索引返回对应的拉丁名
  const names = {
    '森林植被': ['Quercus spp.', 'Pinus spp.', 'Acer spp.', 'Betula spp.'],
    '草原植被': ['Leymus chinensis', 'Stipa spp.', 'Medicago sativa', 'Artemisia spp.'],
    '荒漠植被': ['Opuntia spp.', 'Haloxylon ammodendron', 'Alhagi sparsifolia', 'Salix psammophila'],
    '湿地植被': ['Phragmites australis', 'Typha spp.', 'Scirpus validus', 'Acorus calamus']
  }
  return names[category][index] || 'Plantus scientificus'
}

// 轮播图逻辑
const activeIndex = ref(0)  // 当前激活的轮播图索引
let swiperInstance = null  // Swiper实例

// Swiper初始化回调
function onSwiper(swiper) {
  swiperInstance = swiper
}

// 轮播图切换回调
function onSlideChange(swiper) {
  activeIndex.value = swiper.realIndex
}

// 缩略图悬停处理
function handleThumbHover(idx) {
  activeIndex.value = idx
  if (swiperInstance) {
    swiperInstance.slideToLoop(idx)
  }
}

// 显示二级菜单
function showSubMenu(index) {
  activeSubMenu.value = index
}

// 隐藏二级菜单
function hideSubMenu() {
  activeSubMenu.value = null
}

// 自动滚动植物卡片
const scrollIntervals = []  // 存储滚动定时器
onMounted(() => {
  // 组件挂载后为每行植物卡片启动自动滚动
  plantRows.forEach((_, index) => startScroll(index))
})

// 启动滚动函数
function startScroll(index) {
  const container = document.querySelectorAll('.plant-scroll-container')[index]
  if (!container) return
  
  const list = container.querySelector('.plant-list')
  if (!list) return
  
  let scrollAmount = 0
  const scrollStep = 1
  const scrollWidth = list.scrollWidth / 2  // 滚动宽度为列表宽度的一半
  
  // 设置定时器实现自动滚动
  scrollIntervals[index] = setInterval(() => {
    scrollAmount += scrollStep
    container.scrollLeft = scrollAmount
    
    // 滚动到一半宽度时重置位置
    if (scrollAmount >= scrollWidth) {
      scrollAmount = 0
      container.scrollLeft = 0
    }
  }, 20)
}

// 暂停滚动
function pauseScroll(index) {
  clearInterval(scrollIntervals[index])
}

// 恢复滚动
function resumeScroll(index) {
  startScroll(index)
}

// 搜索功能
function onSearch() {
  if (search.value.trim()) {
    alert(`正在搜索: ${search.value}`)
  }
}

// 分类导航点击处理
function onCategory(cat) {
  activeCategory.value = categories.indexOf(cat)
}
</script>

<style>
/* 导入Swiper基础样式 */
@import 'swiper/css';
@import 'swiper/css/pagination';

/* 定义CSS变量 */
:root {
  --primary-color: #4A785E; /* 主色调 - 森林绿 */
  --primary-light: #9BCC9B; /* 浅绿色 */
  --primary-lighter: #D4E6D9; /* 更浅的绿色 */
  --primary-dark: #3C5148; /* 深绿色 */
  --secondary-color: #6B8E7A; /* 强调色 */
  --accent-color: #A3B899; /* 辅助色 */
  --text-color: #333333; /* 主要文字颜色 */
  --text-secondary: #6B8E7A; /* 次要文字颜色 */
  --light-text: #FFFFFF; /* 白色文字 */
  --bg-gradient-start: #4A785E; /* 背景渐变起始色 */
  --bg-gradient-mid: #9BCC9B; /* 背景渐变中间色 */
  --bg-gradient-end: #D4E6D9; /* 背景渐变结束色 */
  --card-bg: rgba(255, 255, 255, 0.9); /* 卡片背景色 - 半透明白色 */
  --border-color: rgba(106, 142, 122, 0.2); /* 边框颜色 */
  --shadow: 0 4px 12px rgba(60, 81, 72, 0.1); /* 常规阴影 */
  --shadow-hover: 0 8px 20px rgba(60, 81, 72, 0.15); /* 悬停阴影 */
  --radius-sm: 4px;
  --radius-md: 8px;
  --radius-lg: 12px;
}

/* 基础样式重置 */
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

/* 全局body样式 */
body {
  margin: 0;
  padding: 0;
  width: 100%;
  min-height: 100vh;
  background: linear-gradient(
    -45deg,
    #3A6B54,    /* 更深的森林绿 */
    #7BBF6A,    /* 明亮的青绿色 */
    #D4E6D9,    /* 淡绿色 */
    #5D9C7F,    /* 中等蓝绿色 */
    #3A6B54     /* 循环回深绿色 */
  );
  background-size: 400% 400%;
  animation: gradientBG 12s ease infinite;
  font-family: 'Segoe UI', sans-serif;
}

@keyframes gradientBG {
  0% {
    background-position: 0% 50%;
    background-size: 400% 400%;
  }
  50% {
    background-position: 100% 50%;
    background-size: 200% 200%; /* 中途缩小增强流动感 */
  }
  100% {
    background-position: 0% 50%;
    background-size: 400% 400%;
  }
}

/* 容器样式 */
.container {
  width: 100%;
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 20px;
}

.no-padding {
  padding: 0 !important;
}

/* 链接样式 */
a {
  text-decoration: none;
  color: inherit;
  transition: color 0.2s ease;
}

a:hover {
  color: var(--primary-light);
}

/* 图片样式 */
img {
  max-width: 100%;
  height: auto;
  display: block;
}

/* 公告栏样式 */
.announcement-bar {
  background-color: var(--primary-dark);
  color: var(--light-text);
  padding: 8px 0;
  font-size: 14px;
  position: relative;
  overflow: hidden;
  width: 100vw;
  margin-left: calc(-50vw + 50%);
}

.announcement-bar::before {
  content: "";
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: linear-gradient(90deg, transparent, rgba(255,255,255,0.1), transparent);
  transform: translateX(-100%);
  animation: shine 3s infinite;
}

@keyframes shine {
  100% { transform: translateX(100%); }
}

.announcement-text {
  display: inline-block;
  animation: scrollText 20s linear infinite;
  white-space: nowrap;
}

@keyframes scrollText {
  0% { transform: translateX(100%); }
  100% { transform: translateX(-100%); }
}

/* 主头部样式 */
.main-header {
  background: transparent;
  backdrop-filter: blur(8px);
  -webkit-backdrop-filter: blur(8px);
  border-bottom: 1px solid rgba(255,255,255,0.1);
  box-shadow: 0 2px 20px rgba(60, 81, 72, 0.1);
  position: sticky;
  top: 0;
  z-index: 100;
  padding: 15px 0;
  width: 100%;
}

.header-content {
  display: flex;
  justify-content: space-between;
  align-items: center;
  flex-wrap: wrap;
  gap: 20px;
}

.logo-area {
  min-width: 200px;
  flex-shrink: 0;
  display: flex;
  flex-direction: column;
}

.logo {
  font-size: 28px;
  font-weight: 700;
  color: var(--light-text);
  text-shadow: 0 2px 4px rgba(0,0,0,0.2);
  position: relative;
  display: inline-block;
}

.logo::after {
  content: "";
  position: absolute;
  bottom: -5px;
  left: 0;
  width: 100%;
  height: 2px;
  background: var(--light-text);
  transform: scaleX(0);
  transform-origin: right;
  transition: transform 0.3s ease;
}

.logo:hover::after {
  transform: scaleX(1);
  transform-origin: left;
}

.slogan {
  color: rgba(255,255,255,0.8);
  font-size: 14px;
  white-space: nowrap;
}

/* 搜索区域样式 */
.search-area {
  flex: 1;
  min-width: 300px;
  max-width: 600px;
}

.search-box {
  display: flex;
  background: rgba(255,255,255,0.9);
  border: 1px solid rgba(106, 142, 122, 0.3);
  border-radius: var(--radius-lg);
  overflow: hidden;
  box-shadow: var(--shadow);
  width: 100%;
  transition: all 0.3s ease;
}

.search-box:focus-within {
  border-color: var(--primary-light);
  box-shadow: 0 0 0 2px rgba(107, 142, 122, 0.2);
}

.search-box input {
  flex: 1;
  min-width: 150px;
  padding: 10px 15px;
  border: none;
  outline: none;
  font-size: 16px;
  background: transparent;
}

.search-btn {
  background-color: var(--primary-color);
  color: white;
  border: none;
  padding: 0 20px;
  cursor: pointer;
  flex-shrink: 0;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.3s cubic-bezier(0.25, 0.8, 0.25, 1);
}

.search-btn:hover {
  background-color: var(--primary-dark);
  box-shadow: 0 4px 8px rgba(60, 81, 72, 0.2);
}

/* 主导航样式 */
.main-nav {
  background: linear-gradient(to right, rgba(60, 81, 72, 0.9), rgba(74, 120, 94, 0.9));
  backdrop-filter: blur(5px);
  -webkit-backdrop-filter: blur(5px);
  box-shadow: 0 2px 10px rgba(0,0,0,0.1);
  position: relative;
  z-index: 90;
}

.nav-list {
  display: flex;
  list-style: none;
  position: relative;
}

.nav-list > li {
  position: relative;
}

.nav-list > li > a {
  display: block;
  padding: 15px 20px;
  font-weight: 500;
  transition: all 0.3s;
  position: relative;
  color: white;
}

.nav-list > li > a:hover, 
.nav-list > li > a.active {
  background-color: rgba(255, 255, 255, 0.15);
}

.nav-list > li > a::after {
  content: "";
  position: absolute;
  bottom: 0;
  left: 50%;
  width: 0;
  height: 2px;
  background: var(--secondary-color);
  transition: all 0.3s ease;
  transform: translateX(-50%);
}

.nav-list > li > a:hover::after,
.nav-list > li > a.active::after {
  width: 60%;
}

/* 二级菜单样式 */
.sub-menu {
  position: absolute;
  top: 100%;
  left: 0;
  width: 200px;
  background-color: white;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
  border-radius: 0 0 var(--radius-md) var(--radius-md);
  overflow: hidden;
  z-index: 100;
}

.sub-menu li {
  border-bottom: 1px solid #f0f0f0;
}

.sub-menu li:last-child {
  border-bottom: none;
}

.sub-menu a {
  display: block;
  padding: 10px 15px;
  color: var(--text-color);
  transition: all 0.3s;
}

.sub-menu a:hover {
  background-color: #F1F8E9;
  color: var(--primary-dark);
}

/* 二级菜单过渡动画 */
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.3s, transform 0.3s;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
  transform: translateY(-10px);
}

/* 主内容区样式 */
.main-content {
  background-color: rgba(255, 255, 255, 0.85);
  border-radius: 16px;
  margin: 20px auto;
  padding: 30px;
  max-width: 1200px;
  box-shadow: 0 8px 32px rgba(31, 38, 135, 0.15);
  backdrop-filter: blur(8px);
  -webkit-backdrop-filter: blur(8px);
}

/* 轮播区样式 */
.hero-section {
  margin-bottom: 40px;
}

.hero-carousel {
  border-radius: var(--radius-lg);
  overflow: hidden;
  box-shadow: 0 10px 30px rgba(60, 81, 72, 0.2);
  position: relative;
  width: 100%;
  max-width: 1200px;
  margin: 0 auto;
}

.carousel-item {
  position: relative;
  height: 500px;
}

.carousel-image {
  width: 100%;
  height: 100%;
  object-fit: cover;
  object-position: center;
}

.carousel-content {
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  padding: 40px;
  background: linear-gradient(transparent, rgba(60, 81, 72, 0.7));
  color: white;
  transition: all 0.3s ease;
  pointer-events: none; /* 允许鼠标事件穿透 */
  z-index: 5; /* 确保在毛玻璃层下方 */
  text-align: left; /* 确保内容左对齐 */
}

.carousel-title {
  left: 0;
  font-size: 32px;
  margin-bottom: 5px;
  max-width: 80%;
  text-shadow: 0 2px 4px rgba(0, 0, 0, 0.5);
  display: inline-block; /* 确保transform生效 */
  pointer-events: auto; /* 重新启用鼠标事件 */
  transform: translateY(0);
  transition: transform 0.5s ease;
  position: relative;
  z-index: 10; /* 确保在内容层上方 */
}

.hero-carousel:hover .carousel-title {
  transform: translateY(-150px);
}



/* 毛玻璃区域样式 */
.swiper-glass {
  position: absolute;
  left: 0;
  bottom: -200px;
  width: 100%;
  height: 200px;
  background: rgba(105, 121, 114, 0);
  backdrop-filter: blur(12px);
  -webkit-backdrop-filter: blur(12px);
  border-top: 1px solid rgba(232, 16, 16, 0.1);
  display: flex;
  align-items: flex-end;
  padding-bottom: 30px;
  z-index: 10;
  transform: translateY(0);
  transition: all 0.3s ease;
  border: 2px solid transparent;
}


.swiper-glass-hover {
  transform: translateY(-200px);
}

.glass-thumbs {
  width: 100%;
  padding: 0 120px;
  margin-top: 20px;
  display: flex;
  gap: 24px;
  overflow-x: auto;
  scrollbar-width: none;
}

.glass-thumbs::-webkit-scrollbar {
  display: none;
}

.glass-thumb {
  width: 160px;
  height: 90px;
  border-radius: 8px;
  overflow: hidden;
  cursor: pointer;
  border: 2px solid transparent;
  transition: all 0.3s ease;
  position: relative;
  flex-shrink: 0;
  transform-origin: center bottom;
}

.glass-thumb.active, 
.glass-thumb:hover {
  border: 2px solid var(--secondary-color);
  transform: scale(1.15);
  box-shadow: 0 4px 12px rgba(107, 142, 122, 0.3);
  z-index: 1;
}

.glass-thumb img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  display: block;
  transition: transform 0.3s ease;
}

.glass-thumb:hover img {
  transform: scale(1.1);
}

.thumb-title {
  position: absolute;
  bottom: 0;
  left: 0;
  width: 100%;
  padding: 8px;
  background: linear-gradient(transparent, rgba(0, 0, 0, 0.7));
  color: white;
  font-size: 14px;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

/* Swiper分页器样式 */
.swiper-pagination {
  bottom: 100px !important;
}

.swiper-pagination-bullet {
  width: 12px;
  height: 12px;
  background: rgba(255, 255, 255, 0.5);
  opacity: 1;
}

.swiper-pagination-bullet-active {
  background: var(--secondary-color);
  width: 24px;
  border-radius: 6px;
}

/* 植物分类区样式 */
.plant-categories {
  margin-bottom: 40px;
}

.category-row {
  margin-bottom: 40px;
}

.category-row:last-child {
  margin-bottom: 0;
}

.category-title {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 15px;
  padding-bottom: 10px;
  border-bottom: 1px solid var(--border-color);
}

.category-title span {
  font-size: 22px;
  color: var(--primary-dark);
  font-weight: 600;
}

.view-more {
  font-size: 14px;
  color: var(--primary-light);
  transition: color 0.3s;
}

.view-more:hover {
  color: var(--primary-dark);
}

.plant-scroller {
  overflow: hidden;
  width: 100%;
  max-width: 1200px;
  margin: 0 auto;
}

.plant-scroll-container {
  overflow-x: auto;
  scrollbar-width: none;
  padding-bottom: 10px;
}

.plant-scroll-container::-webkit-scrollbar {
  display: none;
}

.plant-list {
  display: inline-flex;
  gap: 15px;
  min-width: 100%;
}

.plant-card {
  flex: 0 0 220px;
  background: var(--card-bg);
  border: 1px solid rgba(106, 142, 122, 0.1);
  border-radius: var(--radius-md);
  overflow: hidden;
  box-shadow: var(--shadow);
  transition: all 0.3s cubic-bezier(0.25, 0.8, 0.25, 1);
}

.plant-card:hover {
  transform: translateY(-5px) scale(1.02);
  box-shadow: 0 10px 20px rgba(60, 81, 72, 0.15);
  border-color: rgba(106, 142, 122, 0.3);
}

.plant-image {
  height: 150px;
}

.plant-image img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.plant-info {
  padding: 15px;
}

.plant-info h4 {
  font-size: 16px;
  margin-bottom: 5px;
  color: var(--primary-dark);
}

.plant-info p {
  font-size: 13px;
  color: var(--text-secondary);
}

/* 资讯区样式 */
.info-section {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 30px;
  margin-bottom: 40px;
  width: 100%;
  max-width: 1200px;
  margin: 0 auto 40px;
}

.section-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 15px;
  padding-bottom: 10px;
  border-bottom: 1px solid var(--border-color);
}

.section-header h3 {
  font-size: 20px;
  color: var(--primary-dark);
}

.section-more {
  font-size: 14px;
  color: var(--primary-light);
  transition: color 0.3s;
}

.section-more:hover {
  color: var(--primary-dark);
}

.video-wrapper,
.news-list {
  background: var(--card-bg);
  border: 1px solid rgba(106, 142, 122, 0.1);
  border-radius: var(--radius-md);
  overflow: hidden;
  box-shadow: var(--shadow);
}

.video-wrapper video {
  width: 100%;
  height: auto;
  display: block;
}

.video-info {
  padding: 15px;
}

.video-info h4 {
  font-size: 18px;
  margin-bottom: 5px;
  color: var(--primary-dark);
}

.video-info p {
  font-size: 14px;
  color: var(--text-secondary);
}

.news-item {
  border-bottom: 1px solid var(--border-color);
  transition: background-color 0.3s;
}

.news-item:last-child {
  border-bottom: none;
}

.news-item a {
  display: flex;
  padding: 15px;
  gap: 15px;
}

.news-item:hover {
  background-color: #F1F8E9;
}

.news-date {
  flex: 0 0 50px;
  font-size: 14px;
  color: var(--primary-light);
  font-weight: 500;
}

.news-title {
  flex: 1;
  font-size: 15px;
}

/* 页脚样式 */
.new-footer {
  position: relative;
  width: 100%;
  background: transparent;
  color: rgb(255, 255, 255);
  margin-top: 60px;
}

.footer-wave {
  position: absolute;
  top: -60px;
  left: 0;
  width: 100%;
  height: 60px;
  background: url('data:image/svg+xml;utf8,<svg viewBox="0 0 1200 120" xmlns="http://www.w3.org/2000/svg" preserveAspectRatio="none"><path d="M0,0V46.29c47.79,22.2,103.59,32.17,158,28,70.36-5.37,136.33-33.31,206.8-37.5C438.64,32.43,512.34,53.67,583,72.05c69.27,18,138.3,24.88,209.4,13.08,36.15-6,69.85-17.84,104.45-29.34C989.49,25,1113-14.29,1200,52.47V0Z" fill="%233C5148" opacity=".25"/><path d="M0,0V15.81C13,36.92,27.64,56.86,47.69,72.05,99.41,111.27,165,111,224.58,91.58c31.15-10.15,60.09-26.07,89.67-39.8,40.92-19,84.73-46,130.83-49.67,36.26-2.85,70.9,9.42,98.6,31.56,31.77,25.39,62.32,62,103.63,73,40.44,10.79,81.35-6.69,119.13-24.28s75.16-39,116.92-43.05c59.73-5.85,113.28,22.88,168.9,38.84,30.2,8.66,59,6.17,87.09-7.5,22.43-10.89,48-26.93,60.65-49.24V0Z" fill="%233C5148" opacity=".5"/><path d="M0,0V5.63C149.93,59,314.09,71.32,475.83,42.57c43-7.64,84.23-20.12,127.61-26.46,59-8.63,112.48,12.24,165.56,35.4C827.93,77.22,886,95.24,951.2,90c86.53-7,172.46-45.71,248.8-84.81V0Z" fill="%233C5148"/></svg>');
  background-size: cover;
}

.footer-content {
  background-color: #3C5148;
  padding: 60px 20px 30px;
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 40px;
  max-width: 1200px;
  margin: 0 auto;
}

.footer-column {
  padding: 0 15px;
}

.footer-title {
  font-size: 18px;
  margin-bottom: 20px;
  position: relative;
  padding-bottom: 10px;
  color: #9BCC9B;
}

.footer-title::after {
  content: "";
  position: absolute;
  bottom: 0;
  left: 0;
  width: 40px;
  height: 2px;
  background: #6B8E7A;
}

.footer-text {
  color: rgba(255,255,255,0.7);
  line-height: 1.6;
  font-size: 14px;
}

.footer-links {
  list-style: none;
  padding: 0;
}

.footer-link {
  display: block;
  padding: 8px 0;
  color: rgba(255,255,255,0.7);
  transition: all 0.3s ease;
  position: relative;
  padding-left: 20px;
}

.footer-link::before {
  content: "→";
  position: absolute;
  left: 0;
  opacity: 0;
  transition: all 0.3s ease;
}

.footer-link:hover {
  color: white;
  padding-left: 25px;
}

.footer-link:hover::before {
  opacity: 1;
}

.contact-item {
  display: flex;
  align-items: center;
  margin-bottom: 15px;
  color: rgba(255,255,255,0.7);
}

.contact-icon {
  width: 18px;
  height: 18px;
  fill: #9BCC9B;
  margin-right: 10px;
}

.footer-bottom {
  background-color: #2C3E32;
  padding: 20px;
  text-align: center;
  font-size: 13px;
  color: rgba(255,255,255,0.6);
}

/* 响应式设计 */

/* 中等屏幕 (992px及以下) */
@media (max-width: 992px) {
  .info-section {
    grid-template-columns: 1fr;
  }
  
  .carousel-item {
    height: 400px;
  }
  
  .carousel-title {
    font-size: 28px;
  }
}

/* 平板尺寸 (768px及以下) */
@media (max-width: 768px) {
  .header-content {
    flex-direction: column;
    align-items: stretch;
    gap: 15px;
  }
  
  .logo-area {
    text-align: center;
    margin-bottom: 10px;
  }
  
  .search-area {
    width: 100%;
    max-width: 100%;
  }
  
  .carousel-item {
    height: 350px;
  }
  
  .carousel-content {
    padding: 20px;
  }
  
  .carousel-title {
    font-size: 24px;
    max-width: 100%;
  }
  
  .plant-card {
    flex: 0 0 180px;
  }
  
  .nav-list {
    flex-wrap: wrap;
  }
  
  .nav-list > li {
    width: 50%;
    text-align: center;
  }
  
  .sub-menu {
    width: 100%;
  }
}

/* 手机尺寸 (576px及以下) */
@media (max-width: 576px) {
  .carousel-item {
    height: 300px;
  }
  
  .carousel-title {
    font-size: 20px;
  }
  
  .category-title span {
    font-size: 18px;
  }
  
  .plant-card {
    flex: 0 0 150px;
  }
  
  .plant-image {
    height: 120px;
  }
  
  .nav-list > li {
    width: 100%;
  }
  
  .glass-thumbs {
    padding: 0 20px;
  }
  
  .glass-thumb {
    width: 120px;
    height: 70px;
    flex-shrink: 0;
    margin-right: 10px;
  }
}
</style>