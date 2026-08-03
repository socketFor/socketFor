---
title: "Python-爬虫项目"
date: 2024-11-10
categories: 
  - "python"
tags: 
  - "python"
coverImage: "3.png"
---

异步API图片获取

```
import asyncio
import aiohttp
import re
from PIL import Image
from io import BytesIO
cunt = 0
uurl = "https://www.dmoe.cc/random.php?return=json"
#“https://api.likepoems.com/img/pc/”
cs = "https://api.likepoems.com/img/pc/"

class PH:

async def geto(ul):
    async with aiohttp.ClientSession() as asy:
        global cunt
        cunt += 1
        incunt = cunt
        print(f"{incunt}准备中")
        try:
            async with await asy.get(ul) as re:
                request = Image.open(BytesIO(await re.content.read()))
                request.save(f'png1/{incunt}.png')
                print(f"{incunt}下完了")
        except Exception as e:
            print(f"ERROR:{e}")

#异步
async def zhidao(ul):
    #引用aiohttp中的ClientSession类
    async with aiohttp.ClientSession() as asy:
        try:
            global cunt
            cunt += 1
            incunt = cunt
            print(f"{incunt}正在连接")
            #调用ClientSession类中的get函数
            async with await asy.get(ul) as req:
                print(f"{incunt}连接成功")
                #获取文本内容
                gethtml = await req.text()
                #对文本内容进行正则处理获取url
                url = re.search(r'"imgurl":"(.*?)"',gethtml).group(1).replace('\\','')
                async with await asy.get(url) as ph:
                    print(f"{incunt}正在下载")
                    #对网站图片转换成二进制并进行储存
                    #ph.content.read()
                    Photo = Image.open(BytesIO(await ph.content.read()))
                    Photo.save(f'png/{incunt}.png')
                    print(f"{incunt}下完了")

        except Exception as e:
            print(e)

async def main1():
    take = [zhidao(uurl) for i in range(20)]
    await asyncio.gather(*take)
async def main2():
    take = [geto(cs) for i in range(20)]
    await asyncio.gather(*take)

if __name__ == '__main__':
    # important
    asyncio.set_event_loop_policy(asyncio.WindowsSelectorEventLoopPolicy())
    asyncio.run(main2())
```
