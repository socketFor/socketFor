---
title: "Python处理学习通题目"
date: 2025-06-21
categories: 
  - "python"
---

1.导出学习通题目html文件

2.改名

![](images/Snipaste_2025-06-21_21-58-26-1024x474.png)

3.运行程序

```
from bs4 import BeautifulSoup
import re
from docx import Document
from pathlib import Path

file_path = Path("output.docx")
if file_path.exists():
    if file_path.is_file():  # 精确判断文件
        print("源文件存在")
        doc = Document("output.docx")
    else:
        print("路径存在，但非文件")
else:
    print("源文件不存在")
    doc = Document()

def extract_fill_in_questions(html_content):
    soup = BeautifulSoup(html_content, 'html.parser')
    # 提取所有填空题
    fill_questions = soup.select("div.mark_item")
    for a in fill_questions:
        c = a.select("div.aiArea")
        for d in c:
            name = d.select("h3.mark_name")
            letter = d.select("ul.mark_letter")
            anser = d.select(".rightAnswerContent")
            for i in name:
                print(i.get_text())
                # 写入到doc中
                doc.add_paragraph(i.get_text())
            if letter:
                for j in letter:
                    print(j.get_text())
                    doc.add_paragraph(j.get_text())
            for k in anser:
                print(k.get_text())
                doc.add_paragraph(k.get_text())
            print("===========================================================")
    doc.save('output.docx')

# 使用示例
if __name__ == "__main__":
    with open('222.html', 'r', encoding='utf-8') as f:
        html_content = f.read()
    extract_fill_in_questions(html_content)
```
