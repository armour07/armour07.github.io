---
title: UnityEditor 基础篇
tags:
  - Unity
  - UnityEditor
  - Editor
categories:
  - Unity
  - Editor
abbrlink: 48080
date: 2018-07-12 12:15:00
---
记录一下常用的编辑器类型，然后把它们集中在一个窗口，以便使用。
### 预览图
![20180712144030.png](/posts/48080/dtw3o27k.jpg)

### 代码部分
##### 一. 建一个类
Editor目录下新建类 **MyLayoutWindow** ,继承 **EditorWindow**。  
编写一个静态方法，用来打开窗口。
```csharp
public class MyLayoutWindow : EditorWindow {
    [MenuItem("Tools/布局窗口")]
    static public void ShowWindow() {
        EditorWindow window = EditorWindow.GetWindow<MyLayoutWindow>();
        window.minSize = new Vector2(400, 450);
        window.maxSize = new Vector2(400, 450);
        window.Show();
    }

    public void OnGUI() {

    }
}
```
这样就可以愉快得在“Tools/布局窗口”下打开窗口了。

##### 二. 开始写OnGUI里面的布局
1. 坐标
为了美观，把二维三维放在同一水平上，所以在前后各加上EditorGUILayout.BeginHorizontal();
EditorGUILayout.EndHorizontal();
即可，后面会频繁用到。
EditorGUILayout.Space();
只是单纯为了中间留点间隔。
```csharp
    private Vector2 mVec2;
    private Vector3 mVec3;
    private Vector4 mVec4;

    public void OnGUI() {
        EditorGUILayout.BeginHorizontal();
        this.mVec2 = EditorGUILayout.Vector2Field("二维坐标", this.mVec2, GUILayout.MaxWidth(150));
        this.mVec3 = EditorGUILayout.Vector3Field("三维坐标", this.mVec3, GUILayout.MaxWidth(250));
        EditorGUILayout.EndHorizontal();
        this.mVec4 = EditorGUILayout.Vector4Field("四维坐标", this.mVec4);
    }
```

2. 颜色
```csharp
    private Color mColor;

    public void OnGUI() {
        this.mColor = EditorGUILayout.ColorField(this.mColor);
    }
```
3. 滑块
```csharp
    private float mSlider;

    public void OnGUI() {
        this.mSlider = EditorGUILayout.Slider(this.mSlider, 0, 10);
    }
```
4. 下拉框
这里需要先定义一个枚举类
```csharp
[Serializable]
public enum ShowType{
    None = 0,
    IntType = 1,
    FloatType = 2,
    StrType = 3
}
```

```csharp
    private int mPopupIndex, mLayer;
    private string[] mPopupStrArr = new[]{
            "第一",
            "第二",
            "第三"
        };
    private int[] mPopupIntArr = new[]{
            0,1,2
        };
    private string mTag;

    public void OnGUI() {
        EditorGUILayout.BeginHorizontal();
        this.mPopupIndex = EditorGUILayout.Popup(this.mPopupIndex, mPopupStrArr, GUILayout.MaxWidth(70));
        EditorGUILayout.LabelField(this.mPopupIndex.ToString(), GUILayout.MaxWidth(30));
        this.mPopupIndex = EditorGUILayout.IntPopup(this.mPopupIndex, mPopupStrArr, mPopupIntArr, GUILayout.MaxWidth(70));
        EditorGUILayout.LabelField(this.mPopupIndex.ToString(), GUILayout.MaxWidth(30));
        //自定义枚举下拉框
        this.mType = (ShowType)EditorGUILayout.EnumPopup(this.mType, GUILayout.MaxWidth(70));
        EditorGUILayout.LabelField(this.mType.ToString(), GUILayout.MaxWidth(70));
        EditorGUILayout.EndHorizontal();
        EditorGUILayout.Space();
        EditorGUILayout.BeginHorizontal();
        //Tag标签下拉框
        EditorGUILayout.LabelField("TagSelect:", GUILayout.MaxWidth(70));
        this.mTag = EditorGUILayout.TagField(this.mTag, GUILayout.MaxWidth(70));
        //Layer层下拉框
        EditorGUILayout.LabelField("LayerSelect:", GUILayout.MaxWidth(80));
        this.mLayer = EditorGUILayout.LayerField(this.mLayer, GUILayout.MaxWidth(70));
        EditorGUILayout.EndHorizontal();
    }
```

5. 提示框
这里不浪费空间，加了个小按钮
```csharp
    public void OnGUI() {
        if (GUILayout.Button("点我")) {
            EditorUtility.DisplayDialog("标题", "提示内容", "确定", "取消");
        }
    ｝
```
6. 勾选+折叠
```csharp
    private bool mIsSelectTog, mIsFoldout, mIsTogGroup;
    private bool[] mIsGroupTogs = new bool[2] { false, false};

    public void OnGUI() {
        this.mIsSelectTog = EditorGUILayout.Toggle("显示折叠", this.mIsSelectTog);
        if (this.mIsSelectTog) {
            this.mIsFoldout = EditorGUILayout.Foldout(this.mIsFoldout, "折叠");
            if (this.mIsFoldout) {
                this.mIsTogGroup = EditorGUILayout.BeginToggleGroup("选择：", this.mIsTogGroup);
                mIsGroupTogs[0] = EditorGUILayout.ToggleLeft("111", mIsGroupTogs[0]);
                mIsGroupTogs[1] = EditorGUILayout.ToggleLeft("222", mIsGroupTogs[1]);
                EditorGUILayout.EndToggleGroup();
            }
        }
    ｝
```
7. 动画片段
```csharp
    private AnimationCurve mCurve = new AnimationCurve();

    public void OnGUI() {
        this.mCurve = EditorGUILayout.CurveField("动画片段", this.mCurve);
    }
```
8. 对象选择框
```csharp
    private Object mObj;
    private Texture mTexture;

    public void OnGUI() {
        //type-Object
        this.mObj = EditorGUILayout.ObjectField("选择object", this.mObj, typeof(Object));
        //type-Texture
        this.mTexture = (Texture)EditorGUILayout.ObjectField("选择图片", this.mTexture, typeof(Texture), true);
    }
```
9. 路径选择
```csharp
    private string mSelectPath;

    public void OnGUI() {
        if (GUILayout.Button("选择", GUILayout.MaxWidth(80))) {
            this.mSelectPath = EditorUtility.OpenFilePanel("选择文件", "", "");
        }
        this.mSelectPath = EditorGUILayout.TextField(this.mSelectPath, GUILayout.MaxWidth(320), GUILayout.MaxHeight(20));
    }
```
10. 提示
```csharp
    public void OnGUI() {
        EditorGUILayout.BeginHorizontal();
        EditorGUILayout.HelpBox("oh my god!!!!!", MessageType.Error);
        EditorGUILayout.HelpBox("oh my god!!!!!", MessageType.Warning);
        EditorGUILayout.HelpBox("oh my god!!!!!", MessageType.Info);
        EditorGUILayout.EndHorizontal();
        EditorGUILayout.HelpBox("oh my god!!!!!", MessageType.None);
    }
```
11. 菜单
![image.png](/posts/48080/zjipg0on.jpg)

```csharp
void OnGUI()
{
    if (GUILayout.Button("test", EditorStyles.toolbarDropDown, GUILayout.Width(100)))
    {
        var menu = new GenericMenu();
        menu.AddItem(new GUIContent("1"), false, ()=>{
            Debug.Log("1");
        });
        menu.AddItem(new GUIContent("2"), false, () => {
            Debug.Log("2");
        });
        menu.AddItem(new GUIContent("3"), false, () => {
            Debug.Log("3");
        });
        menu.ShowAsContext();
    }
}
```
这些只是最最基础的界面显示有关的内容，利用这些，再根据项目的需求，编写工具，才是最终的目的。
