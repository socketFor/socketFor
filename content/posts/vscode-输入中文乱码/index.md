---
title: "vscode 输入中文乱码"
date: 2024-10-05
categories: 
  - "question"
---

![](images/Snipaste_2024-10-05_14-54-45.png)

CTRL+SHIFT+P打开搜索输入：Open Settings (JSON)

在{}中输入：

```
// 配置python语言正常输出中文的环境
"code-runner.executorMap":{ 
    "python":"set PYTHONIOENCODING=utf8 && python"
}, 
```

保存即可
