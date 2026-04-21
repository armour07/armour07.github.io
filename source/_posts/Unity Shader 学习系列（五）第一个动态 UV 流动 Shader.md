---
title: Unity Shader 学习系列（五）第一个动态 UV 流动 Shader
tags:
  - Unity
  - Shader
  - URP
  - HLSL
categories:
  - Unity
  - Shader
abbrlink: 22462
date: 2026-04-21 22:50:00
---

从这一篇开始，我们让贴图“动起来”。
核心思路非常直接：让 UV 随时间持续偏移，这样采样点就会在贴图上不断移动。

## 1. 先新建 Shader 文件

建议在这个目录新建文件：

`Assets/Shaders/Effects/S04_UVScroll.shader`

## 2. 完整代码

```hlsl
Shader "Study/S04_UVScroll"
{
    Properties
    {
        _BaseMap ("Base Map", 2D) = "white" {}
        _BaseColor ("Base Color", Color) = (1, 1, 1, 1)
        _ScrollSpeed ("Scroll Speed", Vector) = (0.2, 0.0, 0.0, 0.0)
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
            float4 _ScrollSpeed;

            Varyings vert(Attributes input)
            {
                Varyings output;
                output.positionCS = TransformObjectToHClip(input.positionOS.xyz);
                output.uv = TRANSFORM_TEX(input.uv, _BaseMap);
                return output;
            }

            half4 frag(Varyings input) : SV_Target
            {
                float2 animatedUV = input.uv + _ScrollSpeed.xy * _Time.y;
                half4 texColor = SAMPLE_TEXTURE2D(_BaseMap, sampler_BaseMap, animatedUV);
                return texColor * _BaseColor;
            }

            ENDHLSL
        }
    }
}
```

## 3. 知识点拆解

### 3.1 为什么 `Scroll Speed` 用 `Vector`

我们只用了 `_ScrollSpeed.xy`，但把它声明成 `Vector` 有两个好处：

- 材质面板里调参更直观，一次就能同时调 U、V 两个方向速度。
- 未来要扩展（比如 z/w 做别的用途）也更方便。

### 3.2 `_Time.y` 是什么

`_Time` 是 Unity 内置时间参数，`_Time.y` 常用于“以秒为单位的线性增长时间值”。

把它乘上速度：

```hlsl
_ScrollSpeed.xy * _Time.y
```

就得到“当前时刻应当偏移多少 UV”。

### 3.3 为什么 UV 连续变化就会有动画

片元每一帧都会重新执行：

```hlsl
animatedUV = input.uv + offset;
```

只要 `offset` 每帧不同，采样位置就每帧不同，最终看到的就是贴图在流动。

### 3.4 方向和速度怎么对应

- `_ScrollSpeed.x > 0`：沿 U 正方向移动。
- `_ScrollSpeed.x < 0`：沿 U 负方向移动。
- `_ScrollSpeed.y` 同理控制 V 方向。
- 绝对值越大，滚动越快。

建议先只调一个轴，确认方向感，再做双轴叠加。

## 4. 跑起来验证

1. 新建材质：`Assets/Materials/M_UVScroll.mat`。
2. Shader 选择：`Study/S04_UVScroll`。
3. 给 `Base Map` 选择一张可平铺纹理（火焰、能量条纹都可以）。
4. 先试 `_ScrollSpeed = (0.3, 0, 0, 0)`，再试 `(0, 0.3, 0, 0)`，观察横向和纵向流动差异。

到这一步，你就已经具备了做“流光面片、护盾能量流、传送门底纹”的基础能力。

## 5. 本篇图片占位

材质展示图：
{% asset_img material-preview.png "material preview" %}

动态效果图：
{% asset_img effect-preview.gif "effect preview" %}

本篇贴图占位：
{% asset_img base-map.png "base-map.png" %}
