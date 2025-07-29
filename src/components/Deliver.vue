<template>
  <div class="eaa-directive">
    <!-- 主标题区域 -->
    <header class="eaa-header">
      <h4>EAA DIRECTIVE (EU) 2019/882</h4>
    </header>

    <!-- 内容容器：左右同宽，紧凑布局 -->
    <div class="content-grid">
      <!-- SGS 提供部分（左侧卡片） -->
      <section class="section sgs-section">
        <h3 class="section-title">Lab Provides</h3>
        <ol class="item-list">
          <li>Test Report</li>
          <li>Verification of Compliance (VoC)</li>
        </ol>
      </section>

      <!-- 申请人准备部分（右侧卡片） -->
      <section class="section applicant-section">
        <h3 class="section-title">Applicant Prepares</h3>
        <ol class="item-list">
          <li>Declaration of Conformity (DoC)</li>
          <li class="split-item">
            <span class="main-text">EAA Technical Documentation</span>
            <span class="secondary-text">(Lab can provide template)</span>
          </li>
        </ol>
      </section>
    </div>
  </div>
</template>

<script setup>
// 无逻辑依赖，纯展示组件
</script>

<style scoped>
.eaa-directive {
  font-family: "Segoe UI", Tahoma, Geneva, Verdana, sans-serif;
  max-width: 1000px;
  margin: 1rem auto; /* 减少外部间距 */
  padding: 0;
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.1); /* 阴影稍弱，更紧凑 */
  border-radius: 6px; /* 圆角减小 */
  overflow: hidden;
}

/* 主标题样式 - 减少内边距 */
.eaa-header {
  background-color: #FCEAE0;
  color: #ed6d2d;
 /* padding: 0.6rem 0.5rem; /* 内边距从2rem 1.5rem 减小 */
  text-align: center;
  position: relative;
  overflow: hidden;
}

.eaa-header::after {
  content: "";
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  height: 3px; /* 高度稍减 */
  background: linear-gradient(
    90deg,
    transparent,
    rgba(255, 255, 255, 0.3),
    transparent
  );
}

.eaa-header h1 {
  margin: 0;
  font-size: 1.5rem; /* 字体稍小 */
  font-weight: 700;
  letter-spacing: 0.3px; /* 字间距减小 */
}

/* 网格布局 - 紧凑间距 */
.content-grid {
  display: grid;
  grid-template-columns: 1fr 1fr; /* 左右同宽 */
  gap: 1rem; /* 卡片间距减小 */
  padding: 1rem; /* 容器内边距减小 */
}

/* section通用样式 - 紧凑内边距 */
.section {
  background: #f8f8f8;
  padding: 1rem; /* 卡片内边距从1.5rem 减小 */
  border-radius: 6px;
  transition: transform 0.3s ease, box-shadow 0.3s ease;
  border: 1px solid #eee;
  min-width: 0;
}

.section:hover {
  transform: translateY(-2px); /* 悬浮位移减小 */
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.08); /* 阴影稍弱 */
}

/* 标题样式 - 紧凑布局 */
.section-title {
  background-color: #838181;
  color: white;
  padding: 0.6rem 0.8rem; /* 标题内边距减小 */
  margin: -1rem -1rem 1rem; /* 与卡片内边距匹配 */
  font-size: 0.9rem; /* 标题字体减小 */
  font-weight: 600;
  border-radius: 6px 6px 0 0;
  position: relative;
}

.section-title::after {
  content: "";
  position: absolute;
  left: 0;
  bottom: 0;
  height: 2px; /* 下划线高度减小 */
  width: 30px; /* 长度稍减 */
  background-color: #ed6d2d;
}

/* 列表样式 - 紧凑间距 */
.item-list {
  list-style: none;
  padding-left: 0;
  margin: 0;
  font-size: 14px;
  counter-reset: list-counter;
}

.item-list li {
  position: relative;
  padding: 0.6rem 0 0.6rem 2rem; /* 列表项内边距减小 */
  border-bottom: 1px solid #eee;
  transition: color 0.2s ease;
  counter-increment: list-counter;
}

.item-list li:last-child {
  border-bottom: none;
  padding-bottom: 0.3rem; /* 最后一项底部内边距减小 */
}

.item-list li::before {
  content: counter(list-counter);
  position: absolute;
  left: 0;
  top: 0.6rem; /* 与列表项内边距匹配 */
  width: 20px; /* 序号圆直径减小 */
  height: 20px;
  background-color: #ed6d2d;
  color: white;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 0.8rem; /* 序号字体减小 */
  font-weight: 600;
}

.item-list li:hover {
  color: #ed6d2d;
}

/* 分割行样式 - 紧凑布局 */
.split-item {
  padding-bottom: 0.8rem; /* 底部内边距减小 */
}

.main-text {
  white-space: nowrap;
  font-size: 0.8rem; /* 主文本稍小 */
}

.secondary-text {
  display: block;
  margin-top: 0.1rem; /* 辅助文本间距减小 */
  font-size: 0.85rem; /* 辅助文本字体减小 */
  color: #555;
  padding-left: 0;
}

/* 响应式调整 - 保持紧凑 */
@media (max-width: 768px) {
  .content-grid {
    grid-template-columns: 1fr;
    gap: 0.8rem; /* 移动端间距更小 */
    padding: 0.8rem;
  }

  .eaa-header h1 {
    font-size: 1.3rem;
  }

  .eaa-directive {
    margin: 0.8rem; /* 移动端外部间距减小 */
    max-width: calc(100% - 1.6rem);
  }

  .main-text {
    white-space: normal;
  }
}
</style>