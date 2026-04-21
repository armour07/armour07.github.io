---
title: Unity Shader 学习系列（七）第一个顶点波浪 Shader
tags:
  - Unity
  - Shader
  - URP
  - HLSL
categories:
  - Unity
  - Shader
abbrlink: 64286
date: 2026-04-21 23:10:00
---

这一篇我们把“动画”从片元阶段挪到顶点阶段：直接改顶点位置，做出波浪起伏。
这是理解“顶点动画”和“像素动画”区别的一篇关键练习。

## 1. 先新建 Shader 文件

建议在这个目录新建文件：

`Assets/Shaders/Effects/S06_VertexWave.shader`

## 2. 完整代码

```hlsl
Shader "Study/S06_VertexWave"
{
    Properties
    {
        _BaseColor ("Base Color", Color) = (1, 1, 1, 1)
        _WaveAmplitude ("Wave Amplitude", Float) = 0.2
        _WaveFrequency ("Wave Frequency", Float) = 2.0
        _WaveSpeed ("Wave Speed", Float) = 2.0
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
            float _WaveAmplitude;
            float _WaveFrequency;
            float _WaveSpeed;

            Varyings vert(Attributes input)
            {
                Varyings output;

                float3 positionOS = input.positionOS.xyz;
                float wave = sin((positionOS.x + positionOS.z) * _WaveFrequency + _Time.y * _WaveSpeed);
                positionOS.y += wave * _WaveAmplitude;

                output.positionCS = TransformObjectToHClip(positionOS);
                return output;
            }

            half4 frag(Varyings input) : SV_Target
            {
                return _BaseColor;
            }

            ENDHLSL
        }
    }
}
```

## 3. 知识点拆解

### 3.1 为什么要在 `vert` 里改顶点

顶点位置属于几何信息，想让网格真正起伏，就必须在顶点阶段改 `positionOS`。
如果只在片元阶段改颜色，看起来会“亮暗变化”，但网格轮廓不会动。

### 3.2 这行波形公式怎么理解

```hlsl
sin((positionOS.x + positionOS.z) * _WaveFrequency + _Time.y * _WaveSpeed)
```

- `(x + z)`：让空间位置参与相位计算，不同顶点产生不同波形。
- `* _WaveFrequency`：控制波长密度，值越大波纹越密。
- `+ _Time.y * _WaveSpeed`：让波形随时间推进。

### 3.3 为什么只改 `y`

```hlsl
positionOS.y += wave * _WaveAmplitude;
```

这表示“沿物体本地 Y 轴上下起伏”。
是最直观的一种波浪写法，适合水面、能量地面、旗帜初版效果。

### 3.4 振幅和频率怎么配合

- `_WaveAmplitude` 决定“起伏高度”。
- `_WaveFrequency` 决定“单位长度内波纹数量”。

如果频率很高但振幅也很高，模型会抖得很乱；建议先固定频率，再慢慢加振幅。

### 3.5 顶点数为什么会影响效果

顶点动画依赖网格顶点密度。
模型面数太低时，波浪会显得很硬、像折线；细分足够时，效果会顺滑很多。

## 4. 跑起来验证

1. 新建材质：`Assets/Materials/M_VertexWave.mat`。
2. Shader 选择：`Study/S06_VertexWave`。
3. 挂到一个细分较多的平面模型上（比如 Plane）。
4. 先试：`Amplitude=0.15`、`Frequency=2`、`Speed=2`，再逐步放大参数。

做完这篇，你就正式踏进“几何层动画”的门了。

## 5. 本篇图片占位

材质展示图：
{% asset_img material-preview.png "material preview" %}

动态效果图：
{% asset_img effect-preview.gif "effect preview" %}
