---
title: UnityEditor 检测预设引用图集数
tags:
  - Unity
  - UnityEditor
  - Editor
categories:
  - Unity
  - Editor
abbrlink: 29867
date: 2018-07-15 11:42:00
---
#### 功能介绍
本工具用于检测预设文件引用的图集数量，并在引用图集数量超过3个时输出警告。

#### 使用方法
1. 在Unity编辑器中，右键点击任意资源文件。
2. 选择菜单项`Assets/检测预设引用图集数`。
3. 工具将自动扫描`Assets/Res/UI`和`Assets/Res/Misc`目录下的预设文件，并输出引用图集数量超过3个的预设。

#### 代码说明
- `CheckSpriteatlasCount`: 主方法，负责遍历预设文件并调用检测逻辑。
- `UpdateProgressBar`: 更新进度条显示，提供更好的用户体验。
- `CheckAtlasCount`: 检测预设文件引用的图集数量，并在超过3个时输出警告。

#### 代码实现
```csharp
[MenuItem("Assets/检测预设引用图集数", priority = 104)]
    static public void CheckSpriteatlasCount()
    {
        string[] ids = AssetDatabase.FindAssets("t:Prefab", new string[] { "Assets/Res/UI", "Assets/Res/Misc" });
        for (int i = 0; i < ids.Length; i++) {
            string path = AssetDatabase.GUIDToAssetPath(ids[i]);
            UpdateProgressBar("Hold On", $"正在检索{path}", (float)i / ids.Length);
            CheckAtlasCount(path);
        }
        EditorUtility.ClearProgressBar();
    }

    static private void UpdateProgressBar(string title, string info, float progress)
    {
        EditorUtility.DisplayProgressBar(title, info, progress);
    }

    static private void CheckAtlasCount(string path)
    {
        // 获取目标依赖的资源
        string[] depends = AssetDatabase.GetDependencies(path);
        var count = 0;
        foreach (var depend in depends) {
            if (depend.EndsWith(".spriteatlas")) {
                count++;
            }
            if(count >= 3) {
                var obj = AssetDatabase.LoadAssetAtPath<UnityEngine.Object>(path);
                Debug.Log("贴图引用大于3", obj);
                break;
            }
        }
    }
```