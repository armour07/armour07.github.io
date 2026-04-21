---
title: Unity Shader 学习系列（二）第一个纯色 Shader
tags:
  - Unity
  - Shader
  - URP
  - HLSL
categories:
  - Unity
  - Shader
abbrlink: 39978
date: 2026-04-21 16:05:00
---

这篇正式进入第一个纯色 Shader：`S01_SolidColor.shader`。  
目标很简单：先让模型稳定显示纯色，同时把最基础的 Shader 结构吃透。

## 1. 先创建文件

新建：

`Assets/Shaders/Basic/S01_SolidColor.shader`

填入代码：

```hlsl
Shader "Study/S01_SolidColor"
{
    Properties
    {
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
            };

            struct Varyings
            {
                float4 positionCS : SV_POSITION;
            };

            float4 _BaseColor;

            Varyings vert(Attributes input)
            {
                Varyings output;
                output.positionCS = TransformObjectToHClip(input.positionOS.xyz);
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

## 2. 逐步拆解

### 2.1 Shader 名称

`Shader "Study/S01_SolidColor"` 是 Shader 菜单路径。  
创建材质后，你可以在 Shader 下拉中按这个路径找到它。

### 2.2 Properties

`_BaseColor ("Base Color", Color) = (1,1,1,1)` 的含义：

1. `_BaseColor`：代码变量名。
2. `"Base Color"`：Inspector 显示名。
3. `Color`：参数类型。
4. `(1,1,1,1)`：默认 RGBA。

`Properties` 里常见参数类型，可以先记住这几个高频：

1. `Color`：颜色（常见于主色、发光色）。
2. `Float`：浮点数（常见于强度、速度、阈值）。
3. `Range(min,max)`：带滑块范围的浮点数（便于美术调参）。
4. `Int`：整数（常见于开关状态或枚举映射）。
5. `Vector`：四维向量（可存方向、参数组）。
6. `2D`：二维纹理（最常见的贴图类型）。

这里先不列太多，够入门就行。后续写到贴图、溶解、动态效果时，我们会一边用一边补充。

{% asset_img inspector-basecolor-placeholder.png "图-材质面板中的 Base Color 参数" %}

### 2.3 SubShader + Tags

```hlsl
"RenderType" = "Opaque"
"RenderPipeline" = "UniversalPipeline"
"Queue" = "Geometry"
```

1. `Opaque`：不透明物体。
2. `UniversalPipeline`：这个 Shader 属于 URP。
3. `Geometry`：走不透明几何队列。

### 2.4 Pass

一个 Pass 可以理解成一次绘制规则。  
这篇只用一个最小 Pass，名字是 `ForwardUnlit`。

### 2.5 #pragma 入口绑定

```hlsl
#pragma vertex vert
#pragma fragment frag
```

1. 顶点阶段入口是 `vert`。
2. 片元阶段入口是 `frag`。

### 2.6 include

```hlsl
#include "Packages/com.unity.render-pipelines.universal/ShaderLibrary/Core.hlsl"
```

引入 URP 的基础工具函数，本篇会用到 `TransformObjectToHClip`。

### 2.7 Attributes（顶点输入）

```hlsl
float4 positionOS : POSITION;
```

1. `positionOS`：物体空间顶点坐标（OS = Object Space）。
2. `POSITION`：语义，标记这是顶点位置输入。

### 2.8 Varyings（阶段间传递）

```hlsl
float4 positionCS : SV_POSITION;
```

1. `positionCS`：裁剪空间坐标。
2. `SV_POSITION`：系统语义，供光栅化阶段定位像素。

### 2.9 顶点函数 vert

```hlsl
output.positionCS = TransformObjectToHClip(input.positionOS.xyz);
```

`TransformObjectToHClip` 的作用：  
把物体空间坐标转换到裁剪空间坐标。

### 2.10 片元函数 frag

```hlsl
half4 frag(Varyings input) : SV_Target
{
    return _BaseColor;
}
```

1. `half4`：四通道颜色输出。
2. `SV_Target`：输出到当前渲染目标。
3. 直接返回 `_BaseColor`，所以结果就是纯色。

## 3. 跑起来验证

1. 新建材质`Materials/S01_SolidColor.mat`，Shader 选 `Study/S01_SolidColor`。
2. 挂到 `Capsule`。
3. 调整 `Base Color`。
4. 模型会实时变色。

{% asset_img basecolor-change-placeholder.gif "动图-拖动 Base Color 实时变色" %}

## 4. 系列规则（后续去重讲解）

从下一篇开始，这些基础点默认不重复展开：

1. `Properties` 基础写法
2. `POSITION / SV_POSITION / SV_Target` 基础语义
3. `#pragma vertex / #pragma fragment`
4. `TransformObjectToHClip` 的基础用途

后续只重点讲“新函数”或“旧函数新用法”。
