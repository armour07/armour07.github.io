---
title: Unity Shader 学习系列（八）第一个 Alpha Clip Shader
tags:
  - Unity
  - Shader
  - URP
  - HLSL
categories:
  - Unity
  - Shader
abbrlink: 21423
date: 2026-04-21 23:20:00
---

这一篇我们开始做“裁剪型透明”。
它不是半透明混合，而是满足条件就显示，不满足就直接丢弃像素，边缘非常干脆。

## 1. 先新建 Shader 文件

建议在这个目录新建文件：

`Assets/Shaders/Effects/S07_AlphaClip.shader`

## 2. 完整代码

```hlsl
Shader "Study/S07_AlphaClip"
{
    Properties
    {
        _BaseMap ("Base Map", 2D) = "white" {}
        _MaskMap ("Mask Map", 2D) = "white" {}
        _BaseColor ("Base Color", Color) = (1, 1, 1, 1)
        _ClipThreshold ("Clip Threshold", Range(0, 1)) = 0.5
    }

    SubShader
    {
        Tags
        {
            "RenderType" = "TransparentCutout"
            "RenderPipeline" = "UniversalPipeline"
            "Queue" = "AlphaTest"
        }

        Pass
        {
            Name "ForwardAlphaClip"

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
            TEXTURE2D(_MaskMap);
            SAMPLER(sampler_MaskMap);

            float4 _BaseMap_ST;
            float4 _MaskMap_ST;
            float4 _BaseColor;
            float _ClipThreshold;

            Varyings vert(Attributes input)
            {
                Varyings output;
                output.positionCS = TransformObjectToHClip(input.positionOS.xyz);
                output.uv = input.uv;
                return output;
            }

            half4 frag(Varyings input) : SV_Target
            {
                float2 baseUV = TRANSFORM_TEX(input.uv, _BaseMap);
                float2 maskUV = TRANSFORM_TEX(input.uv, _MaskMap);

                half4 baseColor = SAMPLE_TEXTURE2D(_BaseMap, sampler_BaseMap, baseUV) * _BaseColor;
                half mask = SAMPLE_TEXTURE2D(_MaskMap, sampler_MaskMap, maskUV).r;

                clip(mask - _ClipThreshold);

                return baseColor;
            }

            ENDHLSL
        }
    }
}
```

## 3. 知识点拆解

### 3.1 为什么 Tag 要改成 `TransparentCutout` 和 `AlphaTest`

这里不是传统半透明，而是“二值裁剪”。
设置成 `TransparentCutout` / `AlphaTest`，渲染队列和管线会按裁剪材质来处理。

### 3.2 `MaskMap` 在这篇里的角色

`BaseMap` 负责颜色，`MaskMap` 负责“哪里保留、哪里裁掉”。
通常用一张灰度图作为遮罩就够了。

### 3.3 为什么常用 `.r` 通道

```hlsl
half mask = SAMPLE_TEXTURE2D(_MaskMap, sampler_MaskMap, maskUV).r;
```

灰度遮罩三个通道通常一样，取 `.r` 最直接。
如果你后面想把不同通道做不同逻辑，再扩展即可。

### 3.4 `clip` 的行为要记牢

```hlsl
clip(mask - _ClipThreshold);
```

当 `mask - threshold < 0` 时，当前像素直接丢弃，不写入颜色。
这就是“硬边裁剪”的本质。

### 3.5 阈值滑动时发生了什么

- 阈值变小：更多像素通过，物体更完整。
- 阈值变大：更多像素被裁掉，物体逐步消失。

这正是后面 Dissolve 的基础机制。

## 4. 跑起来验证

1. 新建材质：`Assets/Materials/M_AlphaClip.mat`。
2. Shader 选择：`Study/S07_AlphaClip`。
3. 指定 BaseMap 和一张灰度 MaskMap。
4. 实时调 `_ClipThreshold`，观察边缘如何被硬裁剪。

这一篇吃透后，你对“裁剪型特效”的底层逻辑就已经拿下了。

## 5. 本篇图片占位

材质展示图：
{% asset_img material-preview.png "material preview" %}

动态效果图：
{% asset_img effect-preview.gif "effect preview" %}

本篇贴图占位：
{% asset_img base-map.png "base-map.png" %}
{% asset_img mask-map.png "mask-map.png" %}
