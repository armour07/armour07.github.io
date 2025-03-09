---
title: Unity Shader 之 clip
tags:
  - Unity
  - Shader
  - clip
categories:
  - Unity
  - Shader
abbrlink: 24077
date: 2019-05-13 10:44:00
---

### 介绍
在Unity Shader中，`clip`函数用于根据条件丢弃像素。它通常用于实现透明效果或裁剪特定区域的像素。当`clip`函数的参数小于0时，当前像素将被丢弃，不会渲染到屏幕上。

### 例子
以下是一个简单的Shader代码示例，展示了如何使用`clip`函数来裁剪像素：

```glsl
Shader "Custom/ClipExample" {
    SubShader {
        Tags { "RenderType"="Opaque" }
        Pass {
            CGPROGRAM
            #pragma vertex vert
            #pragma fragment frag
            #include "UnityCG.cginc"

            struct appdata {
                float4 vertex : POSITION;
            };

            struct v2f {
                float4 pos : SV_POSITION;
            };

            v2f vert(appdata v) {
                v2f o;
                o.pos = UnityObjectToClipPos(v.vertex);
                return o;
            }

            fixed4 frag(v2f i) : SV_Target {
                // 裁剪掉y坐标小于0的像素
                clip(i.pos.y);
                return fixed4(1, 0, 0, 1);
            }
            ENDCG
        }
    }
}
```

