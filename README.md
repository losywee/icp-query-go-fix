# ICP备案查询工具 (Go)

纯 Go 实现的工信部 ICP 备案查询工具，支持域名、App、小程序、快应用的备案查询与违规查询。

原生支持 AI 集成：内置 MCP Server 可被 Claude、Cursor 等 AI Agent 直接调用，附带 Claude Code Skill 实现开箱即用的智能备案查询体验。

Inspired by [ICP_Query](https://github.com/HG-ha/ICP_Query)（Python 版）。

## 效果展示
<img width="696" height="754" alt="image" src="https://github.com/user-attachments/assets/179bce32-9272-495c-8c56-d3e79c9cb4b5" />
<img width="1341" height="764" alt="image" src="https://github.com/user-attachments/assets/3132d9ad-b1d7-4f1c-9835-ecdba4db5e3d" />
<img width="1264" height="1086" alt="img_v3_0210k_deb009ef-4ddb-4bf4-8b1e-28dea4efb2cg" src="https://github.com/user-attachments/assets/fdccc5a3-5762-4d48-b30c-107c7a6f08cf" />
<img width="1554" height="2579" alt="img_v3_0210k_95bba9e9-d9c5-4fdb-8e07-121be5de57dg" src="https://github.com/user-attachments/assets/b0cc76ba-54a9-4292-8878-078bc3ac7b59" />


## 功能特性

- 单条/批量 ICP 备案查询
- 违规域名、App、小程序、快应用查询
- Web UI 界面，支持查询历史与 Excel/JSON 导出
- **MCP Server** — AI Agent 可直接调用备案查询能力
- **Claude Code Skill** — 内置 Skill 文件，配置即用
- 代理池支持（本地 IPv6 / 隧道代理 / API 提取）
- 自动验证码识别与重试
- 纯 Go 实现，跨平台编译

## 安装

### 从 GitHub Release 下载

前往 [Releases](https://github.com/imxw/icp-query-go/releases) 下载对应平台的压缩包，解压后将 `icpcli` 放入 PATH 即可。

### 从源码编译

```bash
git clone https://github.com/imxw/icp-query-go.git
cd icp-query-go
go build -o icpcli .
```

### Docker

```bash
docker build -t icp-query .
docker run -p 8080:8080 icp-query
```

## 使用

### CLI 查询

```bash
# 查询域名备案
icpcli query baidu.com

# 查询 App 备案
icpcli query 微信 -t app

# 查询小程序备案
icpcli query "北京百度网讯科技有限公司" -t mapp

# 查询违规域名
icpcli query baidu.com -t bweb
```

支持的查询类型：

| 类型 | 说明 |
|------|------|
| `web` | 域名备案 |
| `app` | App 备案 |
| `mapp` | 小程序备案 |
| `kapp` | 快应用备案 |
| `bweb` | 违规域名 |
| `bapp` | 违规 App |
| `bmapp` | 违规小程序 |
| `bkapp` | 违规快应用 |

### 批量查询

```bash
# 从文件批量查询
icpcli batch -f domains.txt -t web

# 指定并发数和自动翻页
icpcli batch -f domains.txt -t web -j 10 --auto-page
```

### Web UI

```bash
# 启动 Web 服务（默认端口 8080）
icpcli serve

# 指定端口和配置
icpcli serve -p 8080 -c /path/to/config.yml
```

访问 `http://localhost:8080` 即可使用 Web 界面。

### 版本信息

```bash
icpcli version
icpcli version -o json
```

### MCP Server

```bash
# 启动 MCP Server
icpcli mcp
```

在 Claude Code 中配置：

```bash
# 方式一：命令行添加（icpcli 已在 PATH 中）
claude mcp add icp-query -- icpcli mcp

# 方式二：命令行添加（指定完整路径）
claude mcp add icp-query -- /path/to/icpcli mcp
```

或在项目 `.mcp.json` / `~/.claude/settings.json` 中配置：

```json
{
  "mcpServers": {
    "icp-query": {
      "command": "icpcli",
      "args": ["mcp"]
    }
  }
}
```

MCP 提供以下工具：

| 工具 | 说明 |
|------|------|
| `icp_query` | 备案查询，type: web/app/mapp/kapp |
| `icp_blacklist` | 违规查询，type: bweb/bapp/bmapp/bkapp |
| `config_show` | 查看当前配置 |

### Claude Code Skill

项目内置了 ICP 查询 skill，Claude Code 在项目目录下会自动识别。如需在其他项目中使用，将 `.claude/skills/icp-query/SKILL.md` 复制到目标项目的 `.claude/skills/icp-query/` 目录即可。

## API

Web 服务启动后提供以下 API：

| 方法 | 路径 | 说明 |
|------|------|------|
| GET | `/api/query` | 单条查询 |
| GET | `/api/history` | 查询历史列表 |
| GET | `/api/history/:id` | 历史详情 |
| DELETE | `/api/history/:id` | 删除历史 |
| DELETE | `/api/history` | 清空所有历史 |
| GET | `/api/batch` | 批量任务列表 |
| POST | `/api/batch` | 创建批量任务 |
| GET | `/api/batch/:name` | 批量任务详情 |
| DELETE | `/api/batch/:name` | 删除批量任务 |
| GET | `/api/config` | 当前配置 |

## 免责声明

本项目仅供学习和技术研究使用，不得用于任何商业或非法用途。

本项目通过非官方方式调用工信部 ICP 备案查询接口，可能违反相关服务条款，使用者需自行承担全部风险和责任。作者不对因使用本项目造成的任何直接或间接损失负责。

请遵守相关法律法规，合理使用。

## License

[MIT](LICENSE)
