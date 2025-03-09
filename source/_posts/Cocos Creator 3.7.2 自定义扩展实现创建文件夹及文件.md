---
title: Cocos Creator 自定义扩展实现创建文件夹及文件
tags:
  - Cocos Creator
  - Editor
categories:
  - Cocos Creator
  - Editor
abbrlink: 19237
date: 2023-05-19 11:23:04
---
cocos版本：Cocos Creator 3.7.2
自定义扩展创建流程请参考官方文档 -> [跳转](https://docs.cocos.com/creator/manual/zh/editor/extension/readme.html)

package.json 配置里加入代码生成菜单按钮
```json
    "contributions": {
        "menu": [
            {
                "path": "tools/test",
                "label": "测试",
                "message": "test_click"
            }
        ],
        "messages": {
            "test_click": {
                "methods": ["test_click"]
            }
        }
    },
```

main.ts 中增加点击执行的函数
```typescript
export const methods: { [key: string]: (...any: any) => any } = {
    openPanel() {
        Editor.Panel.open(packageJSON.name);
    },
    async test_click() {
    	// 创建文件夹
        let folder = await Editor.Message.request("asset-db", "create-asset", "db://assets/abc", null);
        if(folder){
            console.log(`create folder ${folder.url}`);
        }
        // 创建文本文件
        let file = await Editor.Message.request("asset-db", "create-asset", "db://assets/abc/test.txt", "abc");
        if(file){
            console.log(`create file ${file.url}`);
        }
    }
};
```

打开 cmd 执行 `npm run build` 后，返回编辑器，打开扩展管理，重新开关扩展以及刷新扩展才能生效。