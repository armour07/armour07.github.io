---
title: Unity Shader 学习系列（十）第一个 Dissolve Shader
tags:
  - Unity
  - Shader
  - URP
  - HLSL
categories:
  - Unity
  - Shader
abbrlink: 63412
date: 2026-04-21 23:40:00
---

这一篇把前面的 `Alpha Clip` 升级成真正可用的 Dissolve。
核心是三步：噪声控制裁剪、边缘带提取、边缘发光叠加。

## 1. 先新建 Shader 文件

建议在这个目录新建文件：

`Assets/Shaders/Effects/S09_Dissolve.shader`

## 2. 完整代码

```hlsl
Shader "Study/S09_Dissolve"
{
    Properties
    {
        _BaseMap ("Base Map", 2D) = "white" {}
        _NoiseMap ("Noise Map", 2D) = "white" {}
        _BaseColor ("Base Color", Color) = (1, 1, 1, 1)

        _DissolveAmount ("Dissolve Amount", Range(0, 1)) = 0.0
        _EdgeWidth ("Edge Width", Range(0.001, 0.2)) = 0.05
        _EdgeColor ("Edge Color", Color) = (1, 0.5, 0.0, 1)
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
            Name "ForwardDissolve"

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
            TEXTURE2D(_NoiseMap);
            SAMPLER(sampler_NoiseMap);

            float4 _BaseMap_ST;
            float4 _NoiseMap_ST;
            float4 _BaseColor;
            float _DissolveAmount;
            float _EdgeWidth;
            float4 _EdgeColor;

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
                float2 noiseUV = TRANSFORM_TEX(input.uv, _NoiseMap);

                half4 baseColor = SAMPLE_TEXTURE2D(_BaseMap, sampler_BaseMap, baseUV) * _BaseColor;
                half noise = SAMPLE_TEXTURE2D(_NoiseMap, sampler_NoiseMap, noiseUV).r;

                float threshold = _DissolveAmount;
                clip(noise - threshold);

                float edge = smoothstep(threshold, threshold + _EdgeWidth, noise);
                float edgeBand = 1.0 - edge;

                half3 finalColor = baseColor.rgb + _EdgeColor.rgb * edgeBand;
                return half4(finalColor, 1.0);
            }

            ENDHLSL
        }
    }
}
```

## 3. 知识点拆解

### 3.1 为什么 Dissolve 要用噪声图

如果只用一个线性阈值，消融边界会很“直线化”。
噪声图能把阈值分布打散，让消融边缘自然破碎，看起来更像烧蚀。

### 3.2 `DissolveAmount` 是总进度

`_DissolveAmount` 从 `0 -> 1`，表示“从几乎不消融到几乎全部消融”。
一般你可以在动画里持续推这个值。

### 3.3 为什么 `clip` 放在前面

```hlsl
clip(noise - threshold);
```

先裁掉不该显示的像素，再处理保留下来的边缘和颜色，逻辑最清晰。

### 3.4 `smoothstep` 在这里不是做透明，而是做边缘带

```hlsl
smoothstep(threshold, threshold + _EdgeWidth, noise)
```

它会把阈值附近的一小段区域平滑化。通过这段平滑值反推出 `edgeBand`，就能得到“只在边缘附近亮起来”的带状区域。

### 3.5 `EdgeWidth` 调的是边缘厚度

- 值小：边缘线细，锐利。
- 值大：边缘线宽，发光区域更厚。

这个参数通常和噪声纹理频率一起看，别单独盲调。

### 3.6 边缘颜色叠加为什么用加法

```hlsl
baseColor.rgb + _EdgeColor.rgb * edgeBand
```

加法会更像“烧边发光”，视觉上更有能量感。
如果用乘法，通常会显得偏暗。

## 4. 跑起来验证

1. 新建材质：`Assets/Materials/M_Dissolve.mat`。
2. Shader 选择：`Study/S09_Dissolve`。
3. 指定 BaseMap 和一张噪声 NoiseMap。
4. 从 `DissolveAmount = 0` 缓慢拉到 `1`，观察消融推进和边缘发光。

这篇做完，你已经能搭一个基础可用的消融特效原型了。

## 5. 本篇图片占位

材质展示图：
{% asset_img material-preview.png "material preview" %}

动态效果图：
{% asset_img effect-preview.gif "effect preview" %}

本篇贴图占位：
{% asset_img base-map.png "base-map.png" %}
{% asset_img noise-map.png "noise-map.png" %}
