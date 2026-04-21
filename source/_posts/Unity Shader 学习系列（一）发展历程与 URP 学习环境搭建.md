---
title: Unity Shader 学习系列（一）发展历程与 URP 学习环境搭建
tags:
  - Unity
  - Shader
  - URP
categories:
  - Unity
  - Shader
abbrlink: 64874
date: 2026-04-21 15:20:00
---

这篇是系列第一篇。先不急着写特效，我们先把大方向搞清楚：  
Unity Shader 这些年怎么演进、为什么换代、现在该学哪套，以及怎么从 0 搭一个靠谱的学习工程。

## 1. Shader 学习最容易踩的坑

最常见的问题是：上来就抄代码，能跑但不知道为什么。  
所以这套系列会按这个节奏走：

1. 先把环境搭好。
2. 再从最小可运行 Shader 开始。
3. 每篇只加一点新知识，稳步推进。

## 2. Unity Shader 发展历程

### 2.1 早期：Built-in 管线时代

常见写法是 `CGPROGRAM`、`Surface Shader`、`UnityCG.cginc`。  
优点是资料多、历史项目多；缺点是可控性和扩展性一般。

### 2.2 后来：SRP 时代

Unity 推出 SRP（Scriptable Render Pipeline）后，主流分成两条：

1. URP：跨平台，性能和画质比较均衡。
2. HDRP：偏高端画质，适合重型项目。

### 2.3 现在：手写 HLSL 和 Shader Graph 并行

1. Shader Graph：效率高，适合快速迭代。
2. 手写 HLSL：可控性强，适合理解底层和做定制效果。

建议顺序：先会手写基础，再用 Graph 提速。

## 3. 为什么会“换代”

核心原因可以总结成三点：

1. 跨平台要求更高（移动端、PC、主机要统一维护）。
2. 渲染能力要持续升级（老体系扩展成本高）。
3. 团队协作要可维护（结构清晰、调试方便）。

## 4. 2026 年推荐学习主线

如果目标是“真正会写 Shader”，建议：

1. URP 手写 Shader（主线）。
2. URP Shader Graph（提效）。
3. Built-in 旧写法（用于看老项目和迁移）。

## 5. Shader 文件结构介绍

很多人第一次看 Shader 会觉得“这文件怎么这么多层大括号”。  
你先记这个思路：**从外到内看，先框架，后细节**。

一个典型的 URP Shader，大致长这样：

```hlsl
Shader "Study/Example"
{
    Properties
    {
        // 材质面板可调参数
    }

    SubShader
    {
        Tags { "RenderPipeline"="UniversalPipeline" }

        Pass
        {
            HLSLPROGRAM
            #pragma vertex vert
            #pragma fragment frag

            // include / struct / 变量 / 函数

            ENDHLSL
        }
    }
}
```

各部分职责可以这么理解：

1. `Shader "路径/名称"`：Shader 的菜单路径和名字。
2. `Properties`：暴露给材质面板调参的入口。
3. `SubShader`：按渲染管线和平台组织的一组渲染实现。
4. `Tags`：告诉引擎“我是谁、怎么参与渲染队列”。
5. `Pass`：一次具体绘制规则（可有多个）。
6. `HLSLPROGRAM...ENDHLSL`：真正执行的顶点/片元代码。
7. `#pragma vertex/#pragma fragment`：绑定入口函数。

后面你会频繁看到这个结构。  
不用一次全记住，先把“层级关系”记住就已经很够用了。

## 6. 从 0 创建 Shader 学习工程

### 6.1 创建项目

1. 打开 Unity Hub。
2. 新建项目，模板选 `Universal 3D`。
3. 项目名建议：`LearnShader`。
4. 选择你习惯管理的目录并创建。

{% asset_img create-project-placeholder.png "图-Unity Hub 创建 URP 工程" %}

### 6.2 检查关键配置

1. `Window > Package Manager`，确认 `Universal RP` 已安装。
2. `Project Settings > Graphics`，确认绑定了 URP Pipeline Asset。
3. 场景里放一个 `Capsule` 和 `Directional Light`，后续调试方便。

{% asset_img graphics-settings-placeholder.png "图-Graphics 中绑定 URP Asset" %}

### 6.3 推荐目录结构

```text
Assets/
  Shaders/
    Basic/
    Effects/
    Lighting/
    Common/
  Materials/
  Textures/
  Scenes/
```

这个结构的好处是：后面系列扩展时不会乱。

## 7. 下一篇预告

下一篇就进代码实战，从 `S01_SolidColor.shader` 开始，按步骤把最基础的 Shader 流程讲透。
