---
title: 微信小程序 常用的 WXSS、CSS 样式及组合
tags:
  - 微信小程序
  - WXSS
  - CSS
categories:
  - 微信小程序
abbrlink: 38379
date: 2025-02-14 09:37:03
---
以下是一些微信小程序中常用的 WXSS、CSS 样式及常用组合，帮助你快速开发界面：

---

### **一、常用布局样式**
#### 1. Flex 布局（最常用）
```css
.container {
  display: flex;
  flex-direction: row; /* 横向排列（默认） */
  justify-content: space-between; /* 两端对齐 */
  align-items: center; /* 垂直居中 */
}

/* 垂直方向布局 */
.vertical-container {
  display: flex;
  flex-direction: column;
  align-items: center;
}

/* 自动填充剩余空间 */
.flex-item {
  flex: 1;
}
```

#### 2. Grid 布局
```css
.grid-container {
  display: grid;
  grid-template-columns: repeat(3, 1fr); /* 三列等宽 */
  gap: 10rpx; /* 间距 */
}
```

---

### **二、常用样式属性**
#### 1. 基础样式
```css
/* 字体与颜色 */
.text {
  font-size: 28rpx;
  color: #333;
  font-weight: bold;
}

/* 边距与内边距 */
.box {
  margin: 20rpx;
  padding: 20rpx;
}

/* 边框 */
.border {
  border: 1rpx solid #e0e0e0;
  border-radius: 8rpx; /* 圆角 */
}

/* 背景与阴影 */
.card {
  background-color: #fff;
  box-shadow: 0 4rpx 12rpx rgba(0, 0, 0, 0.1);
}
```

#### 2. 定位
```css
.relative {
  position: relative;
}

.absolute {
  position: absolute;
  top: 0;
  right: 0;
}

.fixed {
  position: fixed;
  bottom: 40rpx;
  right: 40rpx;
}
```

---

### **三、常用样式组合**
#### 1. 卡片样式
```css
.card {
  padding: 24rpx;
  margin: 20rpx;
  background: #fff;
  border-radius: 12rpx;
  box-shadow: 0 4rpx 8rpx rgba(0, 0, 0, 0.05);
}
```

#### 2. 按钮样式
```css
.primary-button {
  padding: 16rpx 32rpx;
  background: #07c160;
  color: white;
  border-radius: 40rpx;
  text-align: center;
  font-size: 28rpx;
}

/* 禁用状态 */
.button-disabled {
  background: #ddd;
  color: #666;
}
```

#### 3. 输入框样式
```css
.input {
  padding: 20rpx;
  border: 1rpx solid #e0e0e0;
  border-radius: 8rpx;
  font-size: 28rpx;
  width: 100%;
  box-sizing: border-box;
}
```

#### 4. 弹性布局工具类
```css
/* 水平居中 */
.flex-center {
  display: flex;
  align-items: center;
  justify-content: center;
}

/* 两端对齐 */
.flex-between {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
```

#### 5. 文本截断（单行/多行）
```css
/* 单行省略 */
.text-ellipsis {
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

/* 多行省略（2行） */
.text-ellipsis-2 {
  display: -webkit-box;
  -webkit-box-orient: vertical;
  -webkit-line-clamp: 2;
  overflow: hidden;
}
```

---

### **四、常用动画**
```css
/* 渐显动画 */
@keyframes fadeIn {
  from { opacity: 0; }
  to { opacity: 1; }
}

.fade-in {
  animation: fadeIn 0.3s ease-in;
}

/* 缩放动画 */
@keyframes scaleUp {
  from { transform: scale(0.9); }
  to { transform: scale(1); }
}

.scale-up {
  animation: scaleUp 0.2s ease-out;
}
```

---

### **五、注意事项**
1. **单位适配**：推荐使用 `rpx`（响应式像素），微信会自动根据屏幕宽度适配。
2. **全局样式**：可在 `app.wxss` 中定义全局通用样式。
3. **性能优化**：避免过度使用复杂选择器（如层级嵌套过深）。

---

这些样式和组合可以覆盖大部分小程序开发场景，根据实际需求灵活调整即可！