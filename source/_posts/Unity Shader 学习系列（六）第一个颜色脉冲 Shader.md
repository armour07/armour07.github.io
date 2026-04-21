---
title: Unity Shader 学习系列（六）第一个颜色脉冲 Shader
tags:
  - Unity
  - Shader
  - URP
  - HLSL
categories:
  - Unity
  - Shader
abbrlink: 63140
date: 2026-04-21 23:00:00
---

这一篇不依赖贴图，我们直接让颜色做“呼吸感”脉冲。
做 UI 高亮、道具提示、可交互物体闪烁时，这个套路非常常见。

## 1. 先新建 Shader 文件

建议在这个目录新建文件：

`Assets/Shaders/Effects/S05_ColorPulse.shader`

## 2. 完整代码

```hlsl
Shader "Study/S05_ColorPulse"
{
    Properties
    {
        _BaseColor ("Base Color", Color) = (1, 1, 1, 1)
        _PulseSpeed ("Pulse Speed", Float) = 2.0
        _PulseMin ("Pulse Min", Float) = 0.2
        _PulseMax ("Pulse Max", Float) = 1.0
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
            };

            struct Varyings
            {
                float4 positionCS : SV_POSITION;
            };

            float4 _BaseColor;
            float _PulseSpeed;
            float _PulseMin;
            float _PulseMax;

            Varyings vert(Attributes input)
            {
                Varyings output;
                output.positionCS = TransformObjectToHClip(input.positionOS.xyz);
                return output;
            }

            half4 frag(Varyings input) : SV_Target
            {
                float pulse = sin(_Time.y * _PulseSpeed);
                pulse = pulse * 0.5 + 0.5;
                pulse = lerp(_PulseMin, _PulseMax, pulse);
                return _BaseColor * pulse;
            }

            ENDHLSL
        }
    }
}
```

## 3. 知识点拆解

### 3.1 `sin` 为什么能做呼吸动画

`sin` 的输出在 `[-1, 1]` 之间来回变化，本身就是周期函数。
把时间喂进去后，就会得到一个持续往复的波形。

### 3.2 `_PulseSpeed` 控制的是什么

```hlsl
sin(_Time.y * _PulseSpeed)
```

`_PulseSpeed` 越大，波形变化越快，视觉上就是闪烁更快。
建议从 `1~3` 这个区间调，会比较自然。

### 3.3 从 `[-1, 1]` 映射到 `[0, 1]`

```hlsl
pulse = pulse * 0.5 + 0.5;
```

这是个很经典的归一化写法。
因为颜色强度通常不希望出现负值，所以先把波形挪到 `[0,1]`。

### 3.4 `lerp` 在这里的作用

```hlsl
pulse = lerp(_PulseMin, _PulseMax, pulse);
```

`lerp(a, b, t)` 表示在 `a` 和 `b` 之间按 `t` 插值。
这里它把原本 `[0,1]` 的波形，重映射到了你指定的亮度区间 `[PulseMin, PulseMax]`。

### 3.5 为什么建议保留最小值不为 0

如果 `PulseMin = 0`，脉冲最低点会完全黑掉，有些材质看起来会突然“断电”。
设成 `0.2` 或 `0.3` 往往更柔和。

## 4. 跑起来验证

1. 新建材质：`Assets/Materials/M_ColorPulse.mat`。
2. Shader 选择：`Study/S05_ColorPulse`。
3. 先调 `_PulseSpeed = 2`，`_PulseMin = 0.3`，`_PulseMax = 1.0`。
4. 观察脉冲节奏，再把 `BaseColor` 改成偏蓝、偏红，体验不同风格。

这个写法后面还能直接叠加到贴图、Fresnel、边缘光上，组合空间很大。

## 5. 本篇图片占位

材质展示图：
{% asset_img material-preview.png "material preview" %}

动态效果图：
{% asset_img effect-preview.gif "effect preview" %}
