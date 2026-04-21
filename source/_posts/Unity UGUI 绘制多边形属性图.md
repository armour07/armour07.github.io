---
title: Unity UGUI 绘制多边形属性图
tags:
  - Unity
  - UGUI
categories:
  - Unity
  - UI
abbrlink: 4613
date: 2019-12-17 17:12:00
---
### 效果图
{% asset_img 2x6tyzhc.jpg image.jpg %}

### 关于OnPopulateMesh

当一个UI元素生成顶点数据时，会调用OnPopulateMesh(VertexHelper vh)函数，我们可以覆写这个函数，来达到修改顶点数据或获取顶点数据。
其中用到的方法：
方法名称|作用
-|-
Clear|清除流中的所有顶点
AddVert|向流中添加单个顶点
AddTriangle|向缓冲区添加一个三角形

思路：
1. 清空所有顶点信息
2. 添加4个顶点(0, 1, 2, 3)
{% asset_img wm3pqzxc.jpg image.jpg %}

3. 添加三角形 (0, 1, 2), (0, 2, 3), (0, 3, 1)
{% asset_img zim6xipd.jpg image.jpg %}


### 源码
```csharp
using UnityEngine;
using UnityEngine.UI;

public class UIPropWidget : Graphic
{
    /// <summary>
    /// 放大倍数
    /// </summary>
    public int Mul = 100;

    /// <summary>
    /// 旋转值
    /// </summary>
    public float Angle = 0;

    /// <summary>
    /// 顶点值
    /// </summary>
    [Range(0, 1)]
    public float[] Values;

    /// <summary>
    /// 记录顶点
    /// </summary>
    private Vector2[] m_Verts;

    /// <summary>
    /// 顶点数
    /// </summary>
    private int m_Length;

    protected override void Awake()
    {
        base.Awake();
        Values = new float[0];
        m_Verts = new Vector2[0];
    }

    private void Update()
    {
        if(Values.Length != m_Length) {
            m_Length = Values.Length;
            m_Verts = new Vector2[m_Length];
        }
        var cur_pos = Vector2.zero;
        var ave_angle = 360 / m_Length;
        
        for (int i = 0; i < m_Length; i++) {
            float scale = 1;
            if (Values.Length >= i) {
                scale = Values[i];
            }
            var radian = (ave_angle * i + Angle) * Mathf.PI / 180;
            var x = Mathf.Sin(radian) * scale * Mul;
            var y = Mathf.Cos(radian) * scale * Mul;
            m_Verts[i] = new Vector2(cur_pos.x + x, cur_pos.y + y);
        }
        SetVerticesDirty();
    }
    protected override void OnPopulateMesh(VertexHelper vh)
    {
        vh.Clear();
        vh.AddVert(Vector2.zero, color, Vector2.zero);
        foreach (var pos in m_Verts) {
            vh.AddVert(pos, color, Vector2.zero);
        }
        for (int i = 1; i <= m_Length; i++) {
            var num = i + 1 > m_Length ? 1 : i + 1;
            vh.AddTriangle(0, i, num);
        }
    }
}
```
