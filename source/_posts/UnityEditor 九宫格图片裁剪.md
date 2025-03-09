---
title: UnityEditor 九宫格图片裁剪
tags:
  - Unity
  - UnityEditor
  - Editor
categories:
  - Unity
  - Editor
abbrlink: 15906
date: 2018-07-13 15:12:00
---
### 背景介绍
九宫格图片（9-slice scaling）是一种常用于UI设计的图片处理技术，它允许图片在缩放时保持边角的完整性，仅对中间部分进行拉伸。在Unity中，九宫格图片常用于按钮、面板等UI元素。

### 代码解析
本脚本实现了在UnityEditor中对九宫格图片的自动裁剪功能。以下是代码的主要逻辑：
1. **获取选中的图片**：通过`Selection.assetGUIDs`获取用户选中的图片资源。
2. **读取图片边框信息**：通过`sprite.border`获取图片的九宫格边框信息。
3. **计算新图片尺寸**：根据边框信息和`SpritePadding`值计算新图片的尺寸。
4. **复制像素数据**：将原图片的像素数据复制到新图片中，保持边角的完整性。
5. **保存新图片**：将新图片保存为PNG格式，并刷新AssetDatabase。

### 使用说明
1. 在Unity编辑器中，选中需要裁剪的图片资源。
2. 右键点击选中的图片，选择“九宫格图片裁剪”。
3. 脚本会自动处理选中的图片，并在控制台输出处理结果。

### 注意事项
1. 确保选中的图片资源是Sprite类型，并且已经设置了九宫格边框。
2. 如果图片的`SpritePadding`值大于图片的实际可拉伸区域，脚本会跳过该图片的处理。
3. 处理过程中如果发生错误，脚本会在控制台输出错误信息。

### 代码实现
```csharp
using UnityEditor;
using UnityEngine;
using System.IO;

public class CutTextureBySpriteBorder : Editor
{
    private const int SpritePadding = 10; // 九宫格中间预留区域

    [MenuItem("Assets/九宫格图片裁剪", priority = 103)]
    public static void CutTextureBySpriteBorder()
    {
        string[] selectedAssetGuids = Selection.assetGUIDs;
        Debug.Log($"待处理个数：{selectedAssetGuids.Length}");
        bool hasChanges = false;

        foreach (string guid in selectedAssetGuids)
        {
            string assetPath = AssetDatabase.GUIDToAssetPath(guid);
            Sprite sprite = AssetDatabase.LoadAssetAtPath<Sprite>(assetPath);

            if (sprite == null || sprite.border == Vector4.zero) continue;

            Vector4 border = sprite.border;
            int newWidth = (int)(border.x + border.z + SpritePadding);
            int newHeight = (int)(border.y + border.w + SpritePadding);

            Texture2D originalTexture = sprite.texture;
            int originalWidth = originalTexture.width;
            int originalHeight = originalTexture.height;

            int originalPaddingX = (int)(originalWidth - border.x - border.z);
            int originalPaddingY = (int)(originalHeight - border.y - border.w);

            if (SpritePadding >= originalPaddingX || SpritePadding >= originalPaddingY) continue;

            try
            {
                TextureImporter textureImporter = AssetImporter.GetAtPath(assetPath) as TextureImporter;
                TextureImporterSettings settings = new TextureImporterSettings();
                textureImporter.ReadTextureSettings(settings);
                settings.readable = true;
                textureImporter.SetTextureSettings(settings);

                Color32[] originalPixels = originalTexture.GetPixels32();
                Color32[] newPixels = new Color32[newWidth * newHeight];

                CopyPixels(originalPixels, newPixels, border, SpritePadding, originalWidth, newWidth, 0, (int)border.y, 0, (int)border.x);
                CopyPixels(originalPixels, newPixels, border, SpritePadding, originalWidth, newWidth, (int)border.y, (int)border.y + SpritePadding, (int)border.x, (int)border.x + SpritePadding);
                CopyPixels(originalPixels, newPixels, border, SpritePadding, originalWidth, newWidth, (int)border.y + SpritePadding, newHeight, (int)border.x + SpritePadding, newWidth);

                Texture2D newTexture = new Texture2D(newWidth, newHeight);
                newTexture.SetPixels32(newPixels);
                newTexture.Apply();

                byte[] pngData = newTexture.EncodeToPNG();
                File.WriteAllBytes(assetPath, pngData);
                Debug.Log($"重新生成了：{assetPath}", newTexture);

                hasChanges = true;
            }
            catch (System.Exception ex)
            {
                Debug.LogError($"处理图片 {assetPath} 时发生错误: {ex.Message}");
            }
            finally
            {
                settings.readable = false;
                textureImporter.SetTextureSettings(settings);
            }
        }

        if (hasChanges)
            AssetDatabase.Refresh();
    }

    private static void CopyPixels(Color32[] originalPixels, Color32[] newPixels, Vector4 border, int spritePadding, int originalWidth, int newWidth, int startY, int endY, int startX, int endX)
    {
        for (int y = startY; y < endY; y++)
        {
            for (int x = startX; x < endX; x++)
            {
                int newIndex = y * newWidth + x;
                int originalIndex = GetOriginalPixelIndex(y, x, border, spritePadding, originalWidth, newWidth);
                newPixels[newIndex] = originalPixels[originalIndex];
            }
        }
    }

    private static int GetOriginalPixelIndex(int y, int x, Vector4 border, int spritePadding, int originalWidth, int newWidth)
    {
        if (y < border.y)
        {
            if (x < border.x) return y * originalWidth + x;
            if (x < border.x + spritePadding) return y * originalWidth + (int)border.x + (int)((originalWidth - border.x - border.z) / spritePadding * (x - border.x));
            return y * originalWidth + x + (originalWidth - newWidth);
        }
        if (y < border.y + spritePadding)
        {
            if (x < border.x) return ((int)border.y + (int)((originalHeight - border.y - border.w) / spritePadding * (y - border.y))) * originalWidth + x;
            if (x < border.x + spritePadding) return ((int)border.y + (int)((originalHeight - border.y - border.w) / spritePadding * (y - border.y))) * originalWidth + (int)border.x + (int)((originalWidth - border.x - border.z) / spritePadding * (x - border.x));
            return ((int)border.y + (int)((originalHeight - border.y - border.w) / spritePadding * (y - border.y))) * originalWidth + x + (originalWidth - newWidth);
        }
        if (x < border.x) return (y + (originalHeight - newHeight)) * originalWidth + x;
        if (x < border.x + spritePadding) return (y + (originalHeight - newHeight)) * originalWidth + (int)border.x + (int)((originalWidth - border.x - border.z) / spritePadding * (x - border.x));
        return (y + (originalHeight - newHeight)) * originalWidth + x + (originalWidth - newWidth);
    }
}
```