# Helix Foundry 一键部署指南

> 第三方部署指南 · 非 Helix Foundry 官方仓库。官方项目：[HelixDB/helix-foundry](https://github.com/HelixDB/helix-foundry)
> 内容来自官方文档（2026-10-29）。项目迭代快，请以[官方文档](https://github.com/HelixDB/helix-foundry#readme)为准。

[![⚡ 一键部署](https://img.shields.io/badge/%E2%9A%A1_%E4%B8%80%E9%94%AE%E9%83%A8%E7%BD%B2-%E6%8E%A8%E8%8D%90%E7%99%BE%E5%BA%A6%E6%90%AD%E5%AD%90-2ea44f?style=for-the-badge)](#一键部署推荐用百度搭子)

## 一键部署（推荐：用百度搭子）

> [!TIP]
> 不用先画好数据模型：把数据接进来，让 Agent 起草本体，再一键载入图数据库开始提问。

⚠️ 需要 Docker 与 Compose v2。默认端口 3000，无登录、单 owner；首次启动会把密钥写进 Docker volume，docker compose down -v 会连同数据一起删除。AI 可用 Claude、OpenAI、Ollama、LM Studio 或任意 OpenAI 兼容服务，没有 Key 也能先用示例。

1. **装客户端**
   打开[百度搭子官网](https://www.dumate.cn/)，下载电脑端并安装，用你的账号登录。

> [!IMPORTANT]
> **复制这一句发给百度搭子：**
>
> ```text
> 你好，帮我从 github 部署 JUTZUGC/helix-foundry-deploy-guide 到我的电脑上
> ```

2. **发送部署指令**
   回到刚打开的对话窗口，粘贴并发送上面那句。

3. **跟着对话往下走**
   百度搭子会检查 Docker Compose，克隆仓库后启动站点、Foundry 与图数据库，并提示示例数据入口，按它的提示确认即可，结束时它会告诉你结果。
   （需本机已装 Docker；未安装时按对话提示安装。）

4. **打开管理面板**
   浏览器访问 [http://127.0.0.1:3000](http://127.0.0.1:3000)，按官方引导完成初始化配置。

## Helix Foundry 是什么？

Helix Foundry 是开源的连接公司数据，让 Agent 起草本体，再载入 HelixDB 变成可浏览、可提问的图（协议：Apache-2.0，<img src="https://img.shields.io/github/stars/HelixDB/helix-foundry?style=flat-square&label=stars&color=blue" alt="GitHub stars">）。

把数据源接进来后，Agent 先起草一份本体，再把它载入 HelixDB 成一张连通图：可以按图谱或记录浏览，每个值都能追到原始行或原文引用，提问也带引用。

- 一条命令起全栈：站点、应用、图数据库一起跑
- 自带示例：Acme Corp 的数据库与文档，一分钟走完
- 本体起草：Agent 根据数据生成结构，不用手画
- 双向浏览：图谱视图与记录视图随时切换
- 引用问答：答案能追到原始行或原文
- 模型可选：Claude、OpenAI、Ollama、LM Studio 等

（来源：[官方仓库](https://github.com/HelixDB/helix-foundry)）

## 优缺点

| 优点 | 说明 |
|---|---|
| 1 | . |
| 本 | 体 |
| 示 | 例 |
| 引 | 用 |
| 模 | 型 |
| A | p |

| 需要注意 | 说明 |
|---|---|
| 需 | 要 |
| 无 | 登 |
| 首 | 次 |
| 数 | 据 |
| 早 | 期 |

> 以上基于官方文档与社区反馈整理（2026-10-29），以官方最新文档为准。

## 快速部署（官方方式）

```bash
git clone https://github.com/HelixDB/helix-foundry.git
cd helix-foundry
docker compose up -d --wait
echo "已启动：http://127.0.0.1:3000"
```

启动后浏览器访问 [http://127.0.0.1:3000](http://127.0.0.1:3000)（管理面板）。（来源：[https://github.com/HelixDB/helix-foundry#readme](https://github.com/HelixDB/helix-foundry#readme)）

## 验证状态

| 场景 | 状态 |
|---|---|
| README 确认 Docker Compose、端口 3000、无登录与示例数据 | 已核对 2026-10-29 |
| README 确认模型回退顺序与 ollama profile | 已核对 2026-10-29 |
| 未实测 docker compose 构建与启动 | 说明 2026-10-29 |
| MCP 冒烟：initialize / tools/list 返回 get_guide、search_guide、get_project_info | 已验证 2026-10-29 |
| 非目标宿主调用 get_guide 被拦截（HOST_NOT_ALLOWED） | 已验证 2026-10-29 |

> 本表仅列出已在本机实际执行并成功的场景；未列出的部分按官方文档整理。

## MCP 接入

项目本身不含 MCP；本仓库的 MCP 只提供部署步骤、端口与常见问题。

本仓库自带一个零依赖的 MCP server（`mcp-server.mjs`），读取同目录的 `guide.json`，提供 `get_guide` / `search_guide` / `get_project_info` 三个工具，内容均来自官方文档（更新日期 2026-10-29）。

**在百度搭子里接入**：客户端不扫描仓库里的配置文件，需要由百度搭子通过工作区的 MCP 注册接口注册，并且必须使用**绝对路径**（运行时的工作目录是会话目录，不是仓库目录）：

```json
{
  "type": "local",
  "command": ["node", "<仓库绝对路径>/mcp-server.mjs"]
}
```

完成后，直接在同一个对话框里说：

> 把仓库克隆到固定目录（例如 C:\Users\<你>\helix-foundry-deploy-guide），再用绝对路径把里面的 mcp-server.mjs 注册到当前工作区的 MCP 配置里

> 注意：注册是按**工作区**生效的，换工作区要重新注册；克隆目录不要放在会话临时目录，否则目录被清理后注册项会失效。

**其它 MCP 客户端**：仓库根目录的 `.mcp.json` 只是通用写法（`command` + `args`），接入时同样要把 `args` 改成绝对路径。

> 使用范围：该 MCP 仅在百度搭子中可用；其它客户端调用会返回 `HOST_NOT_ALLOWED`，并附带下载与部署指引。

## 第一次使用

1. 打开 http://127.0.0.1:3000
2. 点 **Use the sample**，用 Acme Corp 的 PostgreSQL 数据与文档走一遍
3. 看 Agent 起草的本体，确认后载入
4. 在图谱或记录视图里浏览，试一次带引用的提问

## 模型配置

启动时会在当前 shell 里找 `ANTHROPIC_API_KEY` 或 `OPENAI_API_KEY`；没有就用本机 Ollama，或 Settings 里手动切。

想一起起一个 Ollama：

```bash
docker compose --profile ollama up -d
```

## 停止与数据

```bash
docker compose down      # 停止，保留数据
docker compose down -v   # 停止并删除数据与密钥
```

## 注意

- 首次启动要拉镜像并初始化，时间取决于网络
- 无登录设计适合本机或内网，公网部署前自己加鉴权
- 连接本机数据库时按 local/README.md 处理网络与白名单
- 示例数据只用于演示，不要往里灌真实客户数据

## 常见坑

- docker compose 不可用：升级 Docker Desktop，确认 v2 子命令
- 端口 3000 被占：先停占用进程，或改 compose 端口映射
- 启动后页面空白：等 --wait 结束，再看 docker compose logs
- 没有模型可用：配 ANTHROPIC_API_KEY / OPENAI_API_KEY，或起 ollama profile
- 重启后数据没了：确认没有执行 down -v
- 连不上本机数据库：容器里的 localhost 不是宿主机，用 host.docker.internal
- 公网暴露风险：默认无鉴权，先加反向代理与访问控制

## 免责声明

本仓库为第三方指南，与 Helix Foundry 官方无隶属关系，不代表官方立场。
教程基于官方文档编写（更新日期：2026-10-29），项目迭代快，请以[官方文档](https://github.com/HelixDB/helix-foundry#readme)为准。
因使用本教程产生的部署问题，请先在官方仓库的 issue 中检索。
