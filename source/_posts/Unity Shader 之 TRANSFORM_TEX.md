---
title: Unity Shader 之 TRANSFORM_TEX
tags:
  - Unity
  - Shader
  - TRANSFORM_TEX
categories:
  - Unity
  - Shader
abbrlink: 38220
date: 2019-05-13 16:44:00
---

# TRANSFORM_TEX 宏详解

`TRANSFORM_TEX` 是 Unity Shader 中常用的宏，用于将 UV 坐标与纹理的缩放和平移变换结合。其定义如下：

```c
#define TRANSFORM_TEX(tex, name) (tex.xy * name##_ST.xy + name##_ST.zw)
```

## 参数说明
- `tex`: 输入的 UV 坐标。
- `name`: 纹理的名称，例如 `_MainTex`。

## 使用示例

以下是一个简单的 Shader 示例，展示了如何使用 `TRANSFORM_TEX` 宏：

```glsl
Shader "Custom/ExampleShader"
{
    Properties
    {
        _MainTex ("Texture", 2D) = "white" {}
    }
    SubShader
    {
        Pass
        {
            CGPROGRAM
            #pragma vertex vert
            #pragma fragment frag

            #include "UnityCG.cginc"

            struct appdata
            {
                float4 vertex : POSITION;
                float2 uv : TEXCOORD0;
            };

            struct v2f
            {
                float2 uv : TEXCOORD0;
                float4 vertex : SV_POSITION;
            };

            sampler2D _MainTex;
            float4 _MainTex_ST;

            v2f vert (appdata v)
            {
                v2f o;
                o.vertex = UnityObjectToClipPos(v.vertex);
                o.uv = TRANSFORM_TEX(v.uv, _MainTex);
                return o;
            }

            fixed4 frag (v2f i) : SV_Target
            {
                fixed4 col = tex2D(_MainTex, i.uv);
                return col;
            }
            ENDCG
        }
    }
}
```

## 注意事项
1. `TRANSFORM_TEX` 宏依赖于纹理的 `_ST` 属性（`name##_ST`），它包含了纹理的缩放和平移信息。
2. 确保在 Shader 中定义了 `_MainTex_ST` 或其他纹理的 `_ST` 属性。
3. 如果需要自定义 UV 变换，可以手动实现类似 `TRANSFORM_TEX` 的逻辑。

