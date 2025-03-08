---
title: Unity UGUI 简单实现拖拽
date: 2018-09-04 16:39:00
tags:
- Unity
- UGUI
categories:
- Unity
- UI
---
![效果图.gif](https://upload-images.jianshu.io/upload_images/12848512-b61d15eb9cae1851.gif?imageMogr2/auto-orient/strip)

首先新建脚本DragUI，挂在要拖动的UI上， DragUI实现IPointerDownHandler，IDragHandler接口。
>IPointerDownHandler：鼠标或者触摸点击时回调。
IDragHandler：鼠标或触摸拖拽时回调。

实现代码：
```
using UnityEngine;
using UnityEngine.EventSystems;

public class DragUI : MonoBehaviour, IDragHandler, IPointerDownHandler {

    private Vector2 offsetPos;  //临时记录点击点与UI的相对位置

    public void OnDrag(PointerEventData eventData)
    {
        transform.position = eventData.position - offsetPos;
    }

    public void OnPointerDown(PointerEventData eventData)
    {
        offsetPos = eventData.position - (Vector2)transform.position;
    }
}
```