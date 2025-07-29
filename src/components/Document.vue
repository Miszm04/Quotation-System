<template>
  <div class="document-container">
    <div class="content-grid">
      <!-- 第一个文档项 -->
      <div class="document-item">
        <div class="doc-title" style="font-size: 12px;">
          a) EU 2019/882 测试
          <div>产品技术资料清单_V08</div>
        </div>
        <a
          target="_blank"
          
        >
          
        </a>
      </div>

      <!-- 第二个文档项 -->
      <div class="document-item">
        <div class="doc-title" style="font-size: 12px;">
          b) 型号差异声明
          <div>（如有多型号）</div>
        </div>
        <a
          target="_blank"
          
        >
          
        </a>
      </div>

      <!-- 第三个文档项 -->
      <div class="document-item">
        <div class="doc-title" style="font-size: 12px;">
          c) 用户手册
          <div>（User Manual）</div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.document-container {
  font-family: "Segoe UI", Tahoma, Geneva, Verdana, sans-serif;
  max-width: 1000px;
  margin: 0.2rem auto;
  padding: 0 0.2rem;
}

.content-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 0.5rem;
  padding: 1rem 0;
}

.document-item {
  background: #f8f8f8;
  border-radius: 6px;
  border: 1px solid #eee;
  transition: transform 0.3s ease, box-shadow 0.3s ease;
  overflow: hidden; /* 避免标题圆角溢出 */
}

.document-item:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.08);
}

/* 标题核心样式：#3A3A3A背景+白色字体 */
.doc-title {
  background-color: #838181; /* 统一背景色 */
  color: white; /* 白色字体 */
  padding: 0.6rem 0.8rem; /* 与section-title内边距一致 */
  font-size: 0.95rem; /* 字体大小适配内容 */
  font-weight: 600;
  margin: 0; /* 清除默认margin */
  border-radius: 6px 6px 0 0; /* 顶部圆角与卡片匹配 */
  position: relative;
}

/* 标题底部添加橙色下划线（复用第二个模板的强调色） */
.doc-title::after {
  content: "";
  position: absolute;
  left: 0;
  bottom: 0;
  height: 2px;
  width: 30px;
  background-color: #ed6d2d; /* 与第二个模板标题下划线颜色一致 */
}

/* 文档内容区样式（与标题分隔） */
.document-item > div + * {
  padding: 0.8rem 0.8rem; /* 内容区内边距，与标题形成间距 */
  margin: 0;
}

/* 链接样式保持一致 */
.document-item a {
  color: #ed6d2d;
  text-decoration: none;
  font-size: 0.85rem;
  transition: color 0.2s ease;
}

.document-item a:hover {
  color: #d45a1a;
  text-decoration: underline;
}

/* 响应式调整 */
@media (max-width: 992px) {
  .content-grid {
    grid-template-columns: repeat(2, 1fr);
  }
}

@media (max-width: 768px) {
  .content-grid {
    grid-template-columns: 1fr;
    gap: 0.8rem;
    padding: 0.8rem 0;
  }

  .document-container {
    margin: 0.8rem;
    max-width: calc(100% - 1.6rem);
  }

  .doc-title {
    font-size: 0.9rem; /* 小屏标题字体稍小 */
    padding: 0.5rem 0.7rem;
  }
}
</style>