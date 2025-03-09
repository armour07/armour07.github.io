---
title: Unity Shader 之 UV
tags:
  - Unity
  - Shader
  - UV
categories:
  - Unity
  - Shader
abbrlink: 54291
date: 2019-05-23 15:24:00
---

# Unity Shader 之 UV 详解

## 什么是 UV？

UV 是纹理坐标的简称，用于将2D纹理映射到3D模型上。在 Unity Shader 中，UV 坐标通常表示为 `(u, v)`，其中 `u` 和 `v` 的范围是 `[0, 1]`。UV 坐标决定了纹理的哪一部分会被渲染到模型的特定位置。

## UV 的使用场景

1. **纹理映射**：通过 UV 坐标将纹理贴图应用到模型表面。
2. **动画效果**：通过动态调整 UV 坐标实现纹理滚动、缩放等动画效果。
3. **遮罩效果**：使用 UV 坐标结合遮罩纹理实现复杂的材质效果。

## 示例代码

以下是一个简单的 Unity Shader 示例，展示了如何使用 UV 坐标进行纹理映射：

```hlsl
Shader "Custom/UVShader"
{
    Properties
    {
        _MainTex ("Texture", 2D) = "white" {}
    }
    SubShader
    {
        Tags { "RenderType"="Opaque" }
        LOD 200

        Pass
        {
            CGPROGRAM
            #pragma vertex vert
            #pragma fragment frag

            struct appdata
            {
                float4 vertex : POSITION;
                float2 uv : TEXCOORD0;
            };

            struct v2f
            {
                float2 uv : TEXCOORD0;
                float4 vertex : SV_POSITION;
            };

            sampler2D _MainTex;

            v2f vert (appdata v)
            {
                v2f o;
                o.vertex = UnityObjectToClipPos(v.vertex);
                o.uv = v.uv;
                return o;
            }

            fixed4 frag (v2f i) : SV_Target
            {
                fixed4 col = tex2D(_MainTex, i.uv);
                return col;
            }
            ENDCG
        }
    }
}
```

## 参考资料

- [LearnOpenGL：纹理映射](https://learnopengl.com/Getting-started/Textures)
