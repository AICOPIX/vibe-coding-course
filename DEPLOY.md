# GitHub Pages 部署说明

## 发布目录

静态站点发布源固定为 `网页/`。

## 工作流

仓库使用 GitHub Pages 官方 Actions：

1. 检出仓库
2. 配置 Pages
3. 上传 `网页/` 为 Pages artifact
4. 执行部署

## 发布前检查

- `网页/index.html` 可以直接打开
- 所有课程页都能从首页进入
- 上一节 / 下一节导航有效
- `网页/css/style.css` 正常加载
- 所有路径均为相对路径
- 页面中不存在废弃规则目录引用或本地磁盘路径
