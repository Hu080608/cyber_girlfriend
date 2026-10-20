# GitHub Pages 纯静态客户端

这是可以直接部署到 GitHub Pages 的网页客户端，不需要服务器、不需要 QQ、不需要数据库。

## 部署到 GitHub Pages

1. 创建一个 GitHub 仓库。
2. 把本项目中的 `docs/` 目录上传到仓库（或上传整个项目）。
3. 打开仓库 **Settings → Pages**。
4. **Build and deployment → Source** 选择：
   - **Deploy from a branch**
   - Branch：`main`
   - Folder：`/docs`
5. 点击 **Save**。
6. 等待 1–2 分钟，访问：

```text
https://你的用户名.github.io/仓库名/
```

如果仓库名是 `用户名.github.io`，地址就是：

```text
https://用户名.github.io/
```

> 不需要 GitHub Actions，不需要构建命令。

## 第一次使用

打开页面后进入“设置”：

1. 填写 DeepSeek：
   - API Base：`https://api.deepseek.com/v1`
   - API Key：你的 DeepSeek Key
   - 模型名：`deepseek-chat`
   - 输入/输出价格：默认是示例 ¥2/¥8 每百万 token，可按官网修改
2. 填写免费兜底（可选）：
   - API Base：`https://api.siliconflow.cn/v1`
   - API Key：硅基流动 Key
   - 模型名：`Qwen/Qwen2.5-7B-Instruct`
3. 设置预算上限。
4. 回到“聊天”开始使用。

## 换设备后保持人设和配置：加密 Gist 同步

纯静态页面默认使用浏览器 localStorage，换设备不会自动同步。项目内置了 **GitHub Gist 端到端加密同步**：

### 第一次配置

1. GitHub → Settings → Developer settings → Personal access tokens → Tokens (classic)。
2. 创建 Token，只勾选 **gist** 权限（不要给 repo 权限）。
3. 网页 → 设置 → 跨设备同步：
   - GitHub Classic Token：粘贴 Token
   - 同步密码：至少 8 位，自己记住
   - 自动同步：建议开启
4. 点击“上传 / 创建加密 Gist”。
5. 上传成功后会生成一个 **Gist ID**，把它保存下来。

### 新设备恢复

1. 打开同一个 GitHub Pages 页面。
2. 进入设置 → 跨设备同步。
3. 填写：
   - 同一个 GitHub Token
   - 第一次生成的 Gist ID
   - 同一个同步密码
4. 点击“从 Gist 恢复”。
5. 人设、配置、API Key、记忆、聊天记录、版本和用量会恢复。

### 安全说明

- Gist 内容使用 **PBKDF2 + AES-GCM** 加密；
- GitHub Token 和同步密码只保存在当前浏览器；
- Gist 是未列出（secret）Gist，但请仍然把内容视为公开云存储；
- 忘记同步密码无法恢复；
- 开启自动同步后，本地修改会在约 6 秒后自动加密上传；
- GitHub Token 不要提交到仓库，也不要在公共电脑保存。

## 功能

- 聊天记录：localStorage；
- 人设编辑和版本回滚：localStorage；
- 长期记忆、摘要、手动增删改；
- 自动复盘、待审核进化、diff、审核和回滚；
- token、费用、预算降级；
- 导入/导出 JSON 备份。

所有数据都保存在当前浏览器，不会上传到 GitHub。

## 必须知道的限制

### 1. API Key 无法隐藏

GitHub Pages 只能提供静态文件。你填写的 API Key 会保存在当前浏览器的 `localStorage` 中。

- 不要提交 Key 到仓库；
- 不要在公共电脑填写；
- 清理浏览器缓存会删除 Key 和聊天数据；
- 浏览器端看不到“服务端密钥保护”。

### 2. 可能遇到 CORS

纯静态网页直接调用 DeepSeek/硅基流动时，如果对方没有开放浏览器跨域请求，浏览器会报：

```text
Failed to fetch / CORS
```

GitHub Pages 本身不能做代理。解决方案：

- 使用允许 CORS 的 OpenAI 兼容 API 地址；
- 或者自己部署一个很小的代理（例如 Cloudflare Worker），在页面设置里把 API Base 填成代理地址；
- 不要使用来历不明的公共 CORS 代理，可能窃取 Key 和聊天内容。

可选的最小 Cloudflare Worker 代理示例（不是 GitHub Pages 必需，仅在 CORS 失败时使用）：

```js
export default {
  async fetch(request, env) {
    const cors = {
      "Access-Control-Allow-Origin": "*",
      "Access-Control-Allow-Headers": "Content-Type, Authorization",
      "Access-Control-Allow-Methods": "POST, OPTIONS"
    };
    if (request.method === "OPTIONS") return new Response(null, { headers: cors });
    if (request.method !== "POST") return new Response("Method Not Allowed", { status: 405, headers: cors });
    const upstream = await fetch(env.UPSTREAM, {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
        "Authorization": "Bearer " + env.DEEPSEEK_API_KEY
      },
      body: await request.text()
    });
    const headers = new Headers(upstream.headers);
    Object.entries(cors).forEach(([k, v]) => headers.set(k, v));
    return new Response(upstream.body, { status: upstream.status, headers });
  }
};
```

部署 Worker 时设置：

- `UPSTREAM=https://api.deepseek.com/v1/chat/completions`
- `DEEPSEEK_API_KEY=你的Key`（保存在 Worker Secret，不写进网页）

然后在网页“设置”里把 API Base 改为 Worker 地址，例如：

```text
https://你的worker.workers.dev/v1
```

API Key 栏填任意非空值（如 `proxy`），真正 Key 放在 Worker 环境变量里。

### 3. 不是 7×24 服务


页面关闭后不会在线，也没有 QQ 消息、自动回复、服务器常驻和跨设备同步。

### 4. 浏览器数据

换浏览器、换设备、无痕模式、清理站点数据都会导致历史丢失。请定期使用“导出备份”。

## 隐私

- 默认聊天内容只存在浏览器 localStorage；开启 Gist 同步后会加密上传；
- API 请求直接从浏览器发给你配置的模型供应商；
- 页面本身不包含统计、埋点或第三方 CDN；
- GitHub Pages 可能记录访问日志，但不会收到你的聊天内容。

## 本地打开

因为页面使用了 `fetch` 和 localStorage，建议用本地 HTTP 服务打开，而不是直接双击 `file://`：

```bash
cd docs
python -m http.server 8000
```

然后访问：

```text
http://127.0.0.1:8000/
```

## 文件

```text
docs/index.html   # 页面结构
docs/styles.css   # 样式
docs/app.js       # 聊天、人设、记忆、进化、预算逻辑
docs/README.md    # 本说明
```
