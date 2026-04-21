---
title: Unity Shader 学习系列（三）第一个 Texture Shader
tags:
  - Unity
  - Shader
  - URP
  - HLSL
categories:
  - Unity
  - Shader
abbrlink: 48728
date: 2026-04-21 22:10:00
---

前两篇我们把“纯色 Shader”跑通了，这一篇进入第一个贴图 Shader。  
目标很直接：把一张纹理正确采样到模型上，再和颜色参数做乘法控制。

## 1. 先准备代码文件

新建（或继续使用）：

`Assets/Shaders/Basic/S02_Texture.shader`

示例代码如下（这篇先不做 Tiling/Offset，下一篇再讲）：

```hlsl
Shader "Study/S02_Texture"
{
    Properties
    {
        _BaseMap ("Base Map", 2D) = "white" {}
        _BaseColor ("Base Color", Color) = (1, 1, 1, 1)
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

            Varyings vert(Attributes input)
            {
                Varyings output;
                output.positionCS = TransformObjectToHClip(input.positionOS.xyz);
                output.uv = input.uv;
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

## 2. 这篇新增的知识点（重点）

前两篇已经讲过的内容（`Properties` 基本结构、`Pass`、`#pragma`、`SV_POSITION`、`SV_Target`）这里不重复展开。  
这一篇只看新出现的部分。

### 2.1 `Properties` 里的 `2D`

```hlsl
_BaseMap ("Base Map", 2D) = "white" {}
```

`2D` 表示这是一个二维纹理槽位。  
材质面板里可以把图片拖到这个槽位里，Shader 再通过采样函数读取它。

### 2.2 `TEXCOORD0` 是什么

```hlsl
float2 uv : TEXCOORD0;
```

`TEXCOORD0` 是第一套 UV 通道语义。  
简单理解：模型上每个顶点都带着一组 UV 坐标，这组坐标决定“贴图哪个位置采到当前像素”。

### 2.3 纹理声明：`TEXTURE2D`

```hlsl
TEXTURE2D(_BaseMap);
```

这行是在声明一个纹理资源 `_BaseMap`，对应 `Properties` 里的同名参数。

### 2.4 采样器声明：`SAMPLER`

```hlsl
SAMPLER(sampler_BaseMap);
```

采样器决定“怎么采样”，比如过滤方式、寻址方式等。  
在 URP 里常见写法就是 `TEXTURE2D + SAMPLER` 搭配使用。

### 2.5 采样函数：`SAMPLE_TEXTURE2D`

```hlsl
half4 texColor = SAMPLE_TEXTURE2D(_BaseMap, sampler_BaseMap, input.uv);
```

这是这篇最核心的一句：

1. 从 `_BaseMap` 这张图里采样。
2. 使用 `sampler_BaseMap` 的采样规则。
3. 按 `input.uv` 指定的位置去取颜色。

最终得到 `texColor`。

### 2.6 结果合成：`texColor * _BaseColor`

```hlsl
return texColor * _BaseColor;
```

贴图颜色乘上颜色参数，常用于整体染色或明暗控制。  
当 `_BaseColor` 是白色时，贴图保持原样；改成其他颜色会有整体偏色效果。

{% asset_img texture-color-multiply.png "图-贴图颜色与 BaseColor 相乘效果" %}

## 3. 跑起来验证

1. 新建材质：`Materials/S02_Texture.mat`。
2. Shader 选 `Study/S02_Texture`。
3. 给 `Base Map` 指定一张纹理。
4. 把材质挂到 `Capsule` 或 `Cube`。
5. 调整 `Base Color`，观察整体染色变化。

{% asset_img material-preview.png "图-Texture Shader 调参预览" %}

本篇贴图：
{% asset_img base-map.png "base-map.png" %}

## 4. 小结

这篇你只要吃透三件事就够了：

1. UV 怎么从顶点传到片元。
2. `TEXTURE2D + SAMPLER + SAMPLE_TEXTURE2D` 这一套怎么配合。
3. 为什么很多 Shader 最后都会有一段“贴图颜色 * 参数颜色”。

下一篇我们接着做：**第一个支持 Tiling 和 Offset 的 Texture Shader**。
