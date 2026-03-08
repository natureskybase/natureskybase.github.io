# skybase的小窝

## 部署博客工程

### 1. 克隆仓库

```bash
git clone https://github.com/natureskybase/natureskybase.github.io.git
cd natureskybase.github.io
```

### 2. 安装依赖

需要提前安装 [Node.js](https://nodejs.org/) 和 Hexo CLI：

```bash
npm install -g hexo-cli
npm install
```

### 3. 本地测试

```bash
hexo clean && hexo generate && hexo server
```

浏览器访问 `http://localhost:4000` 预览效果。

### 4. 部署到 GitHub Pages
推送项目将自动启动github page工作流进行部署，使用https://natureskybase.github.io/登陆网页。
