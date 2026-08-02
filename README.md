# Socket Blog

基于 [Hugo](https://gohugo.io/) + [PaperMod](https://github.com/adityatelange/hugo-PaperMod) 构建的个人技术博客，部署于 GitHub Pages。

## 本地开发

```bash
# 克隆仓库
git clone https://github.com/socketFor/my-blog.git
cd my-blog

# 启动开发服务器（热更新）
hugo server -D

# 浏览器访问
# http://localhost:1313/my-blog/
```

## 写文章

```bash
# 创建新文章
hugo new posts/文章标题.md

# 编辑 content/posts/文章标题.md
# draft: true → 本地预览可见，部署后隐藏
# draft: false → 正式发布
```

### 文章中插入图片

将图片和文章放在同一目录下（页面束）：

```
content/posts/文章标题/
  ├── index.md       # 文章内容
  ├── image-1.png    # 图片直接放这里
  └── image-2.png
```

文章中引用：

```markdown
![图片描述](image-1.png)
```

全局共享图片放在 `static/images/` 目录，引用方式：`/images/xxx.png`

## 主题配置

| 文件 | 用途 |
|------|------|
| `hugo.toml` | 站点配置（标题、菜单、SEO 等） |
| `assets/css/extended/custom.css` | 自定义样式 |
| `layouts/` | 主题覆盖模板 |

## 部署

推送 `main` 分支自动触发 GitHub Actions 部署：

```bash
git add .
git commit -m "更新内容"
git push
```

部署状态查看：[Actions](https://github.com/socketFor/my-blog/actions)

## 技术栈

- **框架**：Hugo（Go 静态站点生成器）
- **主题**：PaperMod（轻量响应式主题）
- **部署**：GitHub Pages + GitHub Actions
- **字体**：PingFang SC / Microsoft YaHei（中文），JetBrains Mono（代码）

## 许可

MIT
