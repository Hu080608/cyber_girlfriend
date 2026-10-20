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

## 两个页面入口

部署后的主页面：

```text
https://hu080608.github.io/cyber_girlfriend/
```

完整页面，包含聊天、人设、记忆、进化、用量和设置。

纯净聊天页面：

```text
https://hu080608.github.io/cyber_girlfriend/chat.html
```

只保留聊天内容、输入框和“完整页面”入口，适合日常直接聊天。它和完整页面共用同一份浏览器数据和 Gist 同步设置。

页面已做桌面/平板/手机适配：手机端会隐藏侧栏，输入框适配软键盘和底部安全区，气泡宽度会随屏幕调整。

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

## 真人感和联网查梗

### 更像真人的聊天提示词

页面内置的聊天提示词已经调整：

- 回复更短，通常 1–3 句；
- 先接话/接梗，不总结、不列点、不说教；
- 减少“我理解你的感受”“抱抱你”“无论发生什么”等 AI 套话；
- 限制每句都叫称呼、重复口头禅、括号动作描写；
- 允许轻轻吐槽、反问、有自己的小观点；
- 不认识的梗不装懂，可以自然追问。

### 联网查梗

设置 → 联网查梗 / 热词理解：

| 设置 | 说明 |
|---|---|
| 关闭 | 不搜索 |
| 自动判断 | 只有检测到“梗/什么意思/绷不住/yyds/破防/尊嘟假嘟”等热词时才搜索 |
| 每条消息都搜 | 每次回复前都先检索，最全面但延迟和搜索额度消耗更高 |

搜索服务：

1. **DuckDuckGo**：不需要 Key，但浏览器 CORS 或国内网络可能失败。国内使用不推荐。
2. **Serper.dev**：推荐。注册后获取 API Key，填入“搜索 API Key”；CORS 支持较好。
3. **Tavily**：需要 API Key，适合搜索摘要和问答。

**推荐配置步骤：**

1. 打开 `https://serper.dev/` 注册账号；
2. 在 Dashboard 复制 API Key；
3. 网页设置：
   - 搜索模式：`每条消息都搜`（想更准确）或 `自动判断`
   - 搜索服务：`Serper.dev`
   - 搜索 API Key：粘贴 Key
4. 点击“保存设置”；
5. 点击“测试搜索”；
6. 如果测试结果返回文字，说明搜索链路正常；
7. 回到聊天，发送一句带梗的话测试。

如果“测试搜索”失败，常见原因：

- Key 错误或额度用完；
- 浏览器广告拦截插件拦截了 `google.serper.dev`；
- 网络无法访问 Serper；
- 没有点“保存设置”导致页面还在用旧配置。

**自定义热词词典：**

设置 → 联网查梗 → 自定义热词/梗词典，每行一条：

```text
尊嘟假嘟=真的假的
绷不住了=忍不住笑了/情绪绷不住了
我勒个豆=表示惊讶
```

词典会直接注入她的系统提示，不依赖联网搜索。遇到她总是不懂的固定梗，直接写进这里最稳。

搜索流程：

```text
用户消息
→ 判断是否包含梗/热词
→ 检索“这句话 + 什么意思 梗 网络用语 解释”
→ 提取网页摘要
→ 作为“联网检索参考”注入模型提示词
→ 模型只用于理解，不向用户提“搜索结果显示”
```

- 检索结果缓存 24 小时，减少 API 消耗；
- 搜索失败不会阻止正常聊天；
- 搜索 API Key 只保存在当前浏览器 localStorage；
- 搜索结果是参考资料，可能不准确，模型被要求不要照抄、不要生硬改变语气。

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
