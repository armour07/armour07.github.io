---
title: Unity UGUI 简单实现拖拽
tags:
  - Unity
  - UGUI
categories:
  - Unity
  - UI
abbrlink: 10898
date: 2018-09-04 16:39:00
---
## 前言
Unity UI系统提供了丰富的控件，但有时我们需要实现一些比较简单的效果，比如拖拽。拖拽功能在游戏开发中非常常见，例如实现可拖动的窗口、可移动的按钮或可调整位置的UI元素。本文将详细介绍如何在Unity UGUI中实现拖拽，并提供优化建议和实际应用案例。

## 实现步骤
首先新建脚本DragUI，挂在要拖动的UI上， DragUI实现IPointerDownHandler，IDragHandler接口。
>IPointerDownHandler：鼠标或者触摸点击时回调。
IDragHandler：鼠标或触摸拖拽时回调。

实现代码：
```csharp
using UnityEngine;
using UnityEngine.EventSystems;

public class DragUI : MonoBehaviour, IDragHandler, IPointerDownHandler {

    private Vector2 offsetPos;  //临时记录点击点与UI的相对位置

    public void OnDrag(PointerEventData eventData)
    {
        // 更新UI位置为鼠标/触摸位置减去偏移量
        transform.position = eventData.position - offsetPos;
    }

    public void OnPointerDown(PointerEventData eventData)
    {
        // 计算点击点与UI位置的偏移量
        offsetPos = eventData.position - (Vector2)transform.position;
    }
}
```

### 优化建议
1. **限制拖拽范围**：可以通过设置边界值来限制UI的拖拽范围，防止UI被拖出屏幕。
2. **处理边界情况**：在移动设备上，拖拽功能可能会与屏幕滑动冲突，需要根据实际情况进行调整。
3. **性能优化**：对于频繁拖拽的UI，可以优化代码以减少不必要的计算。

## 总结
本文介绍了Unity UGUI中实现拖拽功能的基本方法，并提供了优化建议。拖拽功能是UI交互中的重要组成部分，掌握其实现方法可以帮助开发者创建更加灵活和用户友好的界面。建议进一步学习Unity的EventSystem和UI组件，以深入理解UI交互的实现原理。