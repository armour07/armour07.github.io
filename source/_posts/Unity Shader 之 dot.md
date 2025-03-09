---
title: Unity Shader 之 dot
tags:
  - Unity
  - Shader
  - dot
categories:
  - Unity
  - Shader
abbrlink: 30166
date: 2019-05-12 14:11:00
---

## 简介

在Unity Shader编程中，dot操作（点积）是一个非常重要的数学运算。它主要用于计算两个向量之间的夹角，或者判断两个向量的方向关系。点积运算在光照计算、法线处理、阴影生成等Shader效果中都有广泛应用。

## 原理

点积的数学定义是两个向量对应分量相乘后再相加。在Shader中，我们可以使用内置的dot函数来计算两个向量的点积。点积的结果可以反映两个向量的方向关系：

- 如果点积大于0，表示两个向量的方向大致相同
- 如果点积等于0，表示两个向量垂直
- 如果点积小于0，表示两个向量的方向大致相反

## 应用

1. **光照计算**：通过计算表面法线和光照方向的点积，可以得到光照强度
2. **法线贴图**：使用点积计算切线空间和世界空间的转换
3. **阴影生成**：通过点积判断表面是否处于阴影区域
4. **边缘检测**：利用点积计算相邻面的法线夹角，实现边缘高亮效果

## 示例

下面是一个简单的Shader代码示例，展示了如何使用dot操作计算漫反射光照：

```glsl
Shader "Custom/DiffuseShader" {
    Properties {
        _MainTex ("Texture", 2D) = "white" {}
    }
    SubShader {
        Tags { "RenderType"="Opaque" }
        LOD 200

        Pass {
            CGPROGRAM
            #pragma vertex vert
            #pragma fragment frag

            #include "UnityCG.cginc"

            struct appdata {
                float4 vertex : POSITION;
                float3 normal : NORMAL;
            };

            struct v2f {
                float4 pos : SV_POSITION;
                float3 worldNormal : NORMAL;
            };

            v2f vert (appdata v) {
                v2f o;
                o.pos = UnityObjectToClipPos(v.vertex);
                o.worldNormal = UnityObjectToWorldNormal(v.normal);
                return o;
            }

            fixed4 frag (v2f i) : SV_Target {
                float3 lightDir = normalize(_WorldSpaceLightPos0.xyz);
                float diff = max(0, dot(i.worldNormal, lightDir));
                return fixed4(diff, diff, diff, 1.0);
            }
            ENDCG
        }
    }
}
```

在这个示例中，我们使用dot操作计算了表面法线和光照方向的点积，得到了漫反射光照强度，并将其作为最终输出颜色。
