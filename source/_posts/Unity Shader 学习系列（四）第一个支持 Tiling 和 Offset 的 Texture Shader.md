---
title: Unity Shader 学习系列（四）第一个支持 Tiling 和 Offset 的 Texture Shader
tags:
  - Unity
  - Shader
  - URP
  - HLSL
categories:
  - Unity
  - Shader
abbrlink: 44833
date: 2026-04-21 22:40:00
---

这一篇我们在“第一个 Texture Shader”的基础上，补上材质里最常用的两个能力：`Tiling` 和 `Offset`。
做完后你会发现，很多“贴图滚动、流光、平铺细节”其实都离不开这一步。

## 1. 先新建 Shader 文件

建议在这个目录新建文件：

`Assets/Shaders/Effects/S03_TextureTilingOffset.shader`

## 2. 完整代码

```hlsl
Shader "Study/S03_TextureTilingOffset"
{
    Properties
    {
        _BaseMap("Base Map", 2D) = "white" {}
        _BaseColor("Base Color", Color) = (1, 1, 1, 1)
    }

    SubShader
    {
        Tags
        {
            "RenderType" = "Opaque"
            "RenderPipeline" = "UniversalPipeline"
            "Queue" = "Geometry"
        }

        Pass
        {
            Name "ForwardUnlit"

            HLSLPROGRAM
            #pragma vertex vert
            #pragma fragment frag

            #include "Packages/com.unity.render-pipelines.universal/ShaderLibrary/Core.hlsl"

            struct Attributes
            {
                float4 positionOS : POSITION;
                float2 uv : TEXCOORD0;
            };

            struct Varyings
            {
                float4 positionCS : SV_POSITION;
                float2 uv : TEXCOORD0;
            };

            TEXTURE2D(_BaseMap);
            SAMPLER(sampler_BaseMap);
            float4 _BaseColor;
            float4 _BaseMap_ST;

            Varyings vert(Attributes input)
            {
                Varyings output;
                output.positionCS = TransformObjectToHClip(input.positionOS.xyz);
                output.uv = TRANSFORM_TEX(input.uv, _BaseMap);
                return output;
            }

            half4 frag(Varyings input) : SV_Target
            {
                half4 texColor = SAMPLE_TEXTURE2D(_BaseMap, sampler_BaseMap, input.uv);
                return texColor * _BaseColor;
            }

            ENDHLSL
        }
    }
}
```

## 3. 知识点拆解

### 3.1 `_BaseMap_ST` 是怎么来的

`_BaseMap_ST` 不需要你在 `Properties` 里手写，它是 Unity 针对 2D 贴图属性自动提供的参数。

- `.xy`：对应 `Tiling`。
- `.zw`：对应 `Offset`。

也就是说，在材质面板里拖动 Base Map 的 Tiling/Offset，本质上就是在改这个 `float4`。

### 3.2 `TRANSFORM_TEX` 到底做了什么

这一行是关键：

```hlsl
output.uv = TRANSFORM_TEX(input.uv, _BaseMap);
```

它的等价公式是：

```hlsl
newUV = input.uv * _BaseMap_ST.xy + _BaseMap_ST.zw;
```

你可以把它理解成“给原始 UV 先缩放，再平移”。

### 3.3 为什么把 UV 变换放在顶点阶段

这里我们在 `vert` 里处理 UV，而不是 `frag` 里处理，主要是因为：

- 逻辑更清晰：顶点阶段把插值所需的数据一次准备好。
- 这类简单 UV 变换在顶点阶段做通常就够用了。

后面做更复杂的逐像素特效时，我们再把更多计算放到片元阶段。

### 3.4 颜色乘法在这里的作用

```hlsl
return texColor * _BaseColor;
```

这个乘法你已经见过了，但这里再强调一下它和 Tiling/Offset 的配合：

- Tiling/Offset 控制“采到贴图哪里”。
- `_BaseColor` 控制“采样后的整体色调和亮度”。

两者组合以后，一个很基础但可用的“材质调参面板”就形成了。

## 4. 跑起来验证

1. 新建材质：`Assets/Materials/M_TextureTilingOffset.mat`。
2. Shader 选择：`Study/TextureTilingOffset`。
3. 给 `Base Map` 指定一张有明显纹理细节的图。
4. 调整 Tiling（比如 `2,2`）和 Offset（比如 `0.3,0.1`），观察纹理如何平铺和偏移。

如果这一步能稳定复现，你后面做流动特效会轻松很多。

## 5. 本篇图片占位

材质展示图：
{% asset_img material-preview.png "material preview" %}

{% asset_img material-preview-2.png "material preview" %}

本篇贴图：
{% asset_img base-map.png "base-map.png" %}
