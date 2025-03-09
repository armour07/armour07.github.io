---
title: Cocos Creator 使用VSCode编译后自动刷新预览
tags:
  - Cocos Creator
  - VSCode
categories:
  - Cocos Creator
abbrlink: 9258
date: 2023-05-15 11:05:14
---
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/e6a6b858733b807ff1de91e3983a01cd.png)
环境：
Cocos Creator 3.7.2
Visual Studio Code 1.78.1
官方详细教程：[跳转](https://docs.cocos.com/creator/manual/zh/scripting/coding-setup.html)

1. 在 Creator 顶部菜单栏点击 **开发者 -> Visual Studio Code 工作流 -> 添加编译任务**，该操作会在项目根目录的 **.vscode** 文件夹下添加 **tasks.json** 任务配置文件。
![1](https://i-blog.csdnimg.cn/blog_migrate/512df4673c6bc1067adc5b326b58f546.png)![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/e12cd36fc50dc46aee2e3e2215e41673.png)
2. 在 VS Code 里按下快捷键 **Ctrl + P**， 输入 task Cocos Creator compile，选择 Cocos Creator compile 回车确认。
   - 首先需要确保操作系统中可以运行 curl 命令，如果在 Windows 操作系统的命令行中运行 curl 提示找不到命令，则需要先安装 curl 到操作系统：[下载地址](http://www.confusedbycode.com/curl/)
   - curl : 无法分析响应内容，因为 Internet Explorer 引擎不可用，或者 Internet Explorer 的首次启动配置不完整。请指定 UseBasicParsing 参数，然后再试一次。
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/2521287bc7377174b40b6ab026daa6fc.png)
3. 任务运行完成，VS Code 窗口下方的输出面板中显示结果，网页也自动触发刷新。

```
StatusCode        : 200
StatusDescription : OK
Content           : success
RawContent        : HTTP/1.1 200 OK
                    Access-Control-Allow-Origin: *
                    Access-Control-Allow-Headers: Content-Type
                    Access-Control-Allow-Methods: *
                    Pragma: no-cache
                    Vary: Accept-Encoding
                    Connection: keep-alive
                    Keep-Aliv...
Forms             :
Headers           : {[Access-Control-Allow-Origin, *], [Access-Control-Allow-Headers, Content-Type], [Access-Control-Allow-Methods, *], [Pragma, no-cache]...}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        :
RawContentLength  : 7
```

