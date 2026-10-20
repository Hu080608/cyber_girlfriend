# 赛博女友·纯静态网页客户端

部署地址（GitHub Pages 完成后）：

https://hu080608.github.io/cyber_girlfriend/

## 说明

- 纯前端静态页面，数据和 API Key 保存在浏览器 localStorage；
- 人设、配置、记忆、聊天记录、进化、预算；
- 支持 GitHub Gist 端到端加密同步，换设备可恢复；
- 不需要服务器、数据库或 QQ。

使用方式见 [docs/README.md](docs/README.md)。

## GitHub Pages

推送 `main` 后，GitHub Actions 会自动部署 `docs/` 到 GitHub Pages。
如果首次未自动部署，请在仓库 Settings → Pages 中确认 Source 为 **GitHub Actions**。
