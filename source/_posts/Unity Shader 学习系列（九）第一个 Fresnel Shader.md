---
title: Unity Shader 学习系列（九）第一个 Fresnel Shader
tags:
  - Unity
  - Shader
  - URP
  - HLSL
categories:
  - Unity
  - Shader
abbrlink: 51447
date: 2026-04-21 23:30:00
---

这一篇做很多特效都会用到的“边缘光”：Fresnel。
简单说，就是视线越贴近表面边缘，亮度越强。

## 1. 先新建 Shader 文件

建议在这个目录新建文件：

`Assets/Shaders/Effects/S08_Fresnel.shader`

## 2. 完整代码

```hlsl
Shader "Study/S08_Fresnel"
{
    Properties
    {
        _BaseColor ("Base Color", Color) = (0.1, 0.2, 0.6, 1.0)
        _FresnelColor ("Fresnel Color", Color) = (0.3, 0.9, 1.0, 1.0)
        _FresnelPower ("Fresnel Power", Float) = 4.0
        _FresnelIntensity ("Fresnel Intensity", Float) = 1.0
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
                float3 normalOS : NORMAL;
            };

            struct Varyings
            {
                float4 positionCS : SV_POSITION;
                float3 normalWS : TEXCOORD0;
                float3 positionWS : TEXCOORD1;
            };

            float4 _BaseColor;
            float4 _FresnelColor;
            float _FresnelPower;
            float _FresnelIntensity;

            Varyings vert(Attributes input)
            {
                Varyings output;

                VertexPositionInputs posInputs = GetVertexPositionInputs(input.positionOS.xyz);
                VertexNormalInputs normalInputs = GetVertexNormalInputs(input.normalOS);

                output.positionCS = posInputs.positionCS;
                output.positionWS = posInputs.positionWS;
                output.normalWS = normalInputs.normalWS;
                return output;
            }

            half4 frag(Varyings input) : SV_Target
            {
                float3 normalWS = normalize(input.normalWS);
                float3 viewDirWS = normalize(GetCameraPositionWS() - input.positionWS);

                float ndv = saturate(dot(normalWS, viewDirWS));
                float fresnel = pow(1.0 - ndv, _FresnelPower) * _FresnelIntensity;

                float3 finalColor = _BaseColor.rgb + _FresnelColor.rgb * fresnel;
                return half4(finalColor, 1.0);
            }

            ENDHLSL
        }
    }
}
```

## 3. 知识点拆解

### 3.1 为什么 Fresnel 要用法线

Fresnel 本质是“法线方向和视线方向夹角”驱动的效果。
所以除了位置，这篇第一次必须把 `normalOS` 从顶点输入带进来。

### 3.2 `GetVertexPositionInputs` 和 `GetVertexNormalInputs` 的意义

这两个 URP 封装函数会帮你把对象空间数据转到常用空间：

- `positionWS`：世界空间位置。
- `normalWS`：世界空间法线。
- `positionCS`：裁剪空间位置（用于光栅化）。

相比手写矩阵乘法，初学阶段更稳、更不容易出错。

### 3.3 视线方向是怎么求的

```hlsl
GetCameraPositionWS() - input.positionWS
```

从像素指向相机的向量就是视线方向。归一化后才能参与点乘。

### 3.4 `dot(normal, viewDir)` 代表什么

点乘值越接近 1，说明视线越正对表面；越接近 0，越接近擦边看。
Fresnel 一般就是在“擦边”时变亮，所以用了：

```hlsl
1.0 - ndv
```

### 3.5 `pow` 对边缘分布的影响

`pow(x, power)` 会改变曲线陡峭程度。

- `power` 小：边缘光扩散更宽。
- `power` 大：边缘光更细、更集中。

这就是 `_FresnelPower` 的调参意义。

### 3.6 最终颜色怎么叠加

```hlsl
_BaseColor + _FresnelColor * fresnel
```

你可以理解为：先有一个基础底色，再在边缘区域额外叠一层发光色。
这是最常见、也最容易控制的写法。

## 4. 跑起来验证

1. 新建材质：`Assets/Materials/M_Fresnel.mat`。
2. Shader 选择：`Study/S08_Fresnel`。
3. 把材质挂到球体上，旋转相机观察边缘高光变化。
4. 重点调 `_FresnelPower` 和 `_FresnelIntensity`，看“边缘宽度”和“亮度”分别怎么变。

这篇掌握后，你就能做非常多“护盾、描边、能量体”效果。

## 5. 本篇图片占位

材质展示图：
{% asset_img material-preview.png "material preview" %}

动态效果图：
{% asset_img effect-preview.gif "effect preview" %}
