---
title: "NoneBot 配置文件"
date: 2024-09-30
categories: 
  - "play"
coverImage: "11.jpg"
---

### bot.py

```
import nonebot
from nonebot.adapters.onebot.v11 import Adapter  # 避免重复命名

# 初始化 NoneBot
nonebot.init()

# 注册适配器
driver = nonebot.get_driver()
driver.register_adapter(Adapter)

# 在这里加载插件
nonebot.load_builtin_plugins("echo")  # 内置插件
nonebot.load_plugins("src/plugins")  # 本地插件

if __name__ == "__main__":
    nonebot.run()
```

### .env

```
ENVIRONMENT=dev
COMMON_CONFIG=common config
DRIVER=~fastapi+~httpx+~websockets
SUPERUSERS=["2067965693"]
HOST=127.0.0.1  # 配置 NoneBot 监听的 IP / 主机名
PORT=59850  # 配置 NoneBot 监听的端口
COMMAND_START=[""]  # 配置命令起始字符
COMMAND_SEP=["."]  # 配置命令分割字符
```

### 参考：

[https://blog.csdn.net/iteapoy/article/details/141254725](https://blog.csdn.net/iteapoy/article/details/141254725)
