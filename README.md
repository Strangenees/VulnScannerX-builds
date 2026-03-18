# VulnScannerX

**VulnScannerX** 是一款高性能、模块化的多协议服务漏洞扫描器，集成指纹识别、主动协议探测、漏洞验证等能力，适用于资产测绘、安全巡检、红蓝对抗等场景。

> 当前版本支持静态嵌入与动态加载指纹/POC，具备插件扩展机制与 YAML 格式规则支持，并内置对腾讯云函数（SCF）代理转发的支持，可将指纹/POC HTTP 请求通过云函数中转，降低源站暴露风险。

---

## ✨ 项目特点

- 🚀 **多协议识别**  
  内置主动协议识别模块，支持 HTTP(S)、FTP、SSH、Redis、MySQL、RDP、SMB、MongoDB、VNC 等常见服务类型。

- 🌐 **Web GUI 可视化**  \
  提供现代化的 Web 界面（`VulnScannerX web`），支持任务管理、资产检索、漏洞详情查看及自定义 POC 编写，操作体验更佳。

- 🧩 **分布式 Worker 集群**  \
  通过 `VulnScannerX worker` 将多台主机注册为 Worker 节点，由 Web 控制端统一调度 URL/Web 扫描任务，自动上报 CPU/内存/负载并按容量智能分配任务，适合大规模资产巡检与红队长时间摸底。

- ♻️ **外置规则一致性同步**  \
  Worker 会周期拉取主节点外置规则快照（`plugins/pocs`、`plugins/fingerprints`）并自动热重载，保证多节点规则一致，避免“主节点可识别、Agent 不识别”。

- 🔍 **搜索引擎联动**  \
  集成 **FOFA**、**Hunter**、**Quake**、**ZoomEye**、**Shodan** 五大测绘引擎，支持从云端检索资产并自动下发扫描任务，实现"查询即扫描"的自动化闭环。

- 🔑 **敏感信息收集（面向红队）**  \
  内置正则驱动的敏感信息引擎（邮箱、云 AK/SK、Webhook、JWT、DB URI 等），可在 Web 任务中按需开启并精细控制。

- 🪪 **License 离线可用**  \
  采用“本地签名校验 + 在线优先 + 离线回退”机制：在线可校验授权等级并缓存，离线场景可使用载荷级别或本地缓存继续运行；无可用等级时回退 `free` 配额。

- 🔌 **模块化插件引擎**  
  - **指纹识别**：支持基于路径、响应头、Body 特征、Favicon 哈希等多维特征识别。
  - **POC 扫描**：兼容 YAML 格式 POC（支持动态加载），内置大量高危漏洞规则；支持 GUI 在线新增/编辑外部 POC（JSON 编辑并保存为 YAML），内置 POC 只读。

- 🕵️ **OPSEC 行为控制**  
  内建 `normal`/`stealth` 模式，智能调节扫描并发、请求抖动（Jitter）及 User-Agent 轮询，有效规避 WAF/流量審計系统的检测。

- 🧱 **扫描范围护栏**  
  在系统设置中可配置 CIDR / Host / Port 的 allow/deny 规则，扫描前会按护栏过滤目标与端口，降低误扫与越界风险。

- ⚡ **高性能并发**  
  基于 Go 协程池的自适应调度机制，支持大规模资产快速巡检。

- 📄 **结构化结果导出**  
  在 Web 界面支持资产结果的 JSON / CSV 导出，便于结果归档与二次处理。

- 📢 **任务结果推送（方糖通知）**  \
  支持通过「方糖」(Server 酱 Turbo) 对 Web 任务扫描结果进行推送：任务完成时发送资产/漏洞/敏感信息摘要，发现高危漏洞时额外发送告警，便于做持续监控与值班通知。

---

## 📦 下载与校验

该仓库提供预编译二进制压缩包，不包含源码。

### 可用构建产物

| 平台 | 文件名 |
| --- | --- |
| macOS amd64 | `VulnScannerX-darwin-amd64.zip` |
| macOS arm64 | `VulnScannerX-darwin-arm64.zip` |
| Linux 386 | `VulnScannerX-linux-386.zip` |
| Linux amd64 | `VulnScannerX-linux-amd64.zip` |
| Linux arm64 | `VulnScannerX-linux-arm64.zip` |
| Windows 386 | `VulnScannerX-windows-386.zip` |
| Windows amd64 | `VulnScannerX-windows-amd64.zip` |

每个压缩包内包含对应平台的可执行文件、`plugins/` 目录以及一份随包说明文档。

### SHA256

仓库根目录提供 [`SHA256SUMS.txt`](./SHA256SUMS.txt)：

```text
b903fd957cdae09faea73e0b446b8baa8e031213fb5dbea0461cfad97c8513b6  VulnScannerX-darwin-amd64.zip
75d43f37c63231ac1056b339b42e2eea2e3f671544dca1ed169a579f3c851682  VulnScannerX-darwin-arm64.zip
6151b5edbfe1a0aeab429c8624ffea0276c0ef5632c17aac0aa9e695e5dad41f  VulnScannerX-linux-386.zip
d5f1bda65508e8b6e2c418c71650f3afa02591ea03c1ab297c1a66534385ab93  VulnScannerX-linux-amd64.zip
0c540aab194db9e2f32c2a28ced6f5ed51e698a9143f4413dedd4e5fcf097566  VulnScannerX-linux-arm64.zip
121788f5b76b6a366e2846758ef0735b190d056b952c804ab7dd5549c6d8c4df  VulnScannerX-windows-386.zip
5df47af8315d83f64101ca62d4182dd31285a107d048f8392c2020259db71124  VulnScannerX-windows-amd64.zip
```

Linux / macOS 可使用如下命令校验：

```bash
sha256sum -c SHA256SUMS.txt
```

### 快速启动

以下示例以 `Linux amd64` 包为例：

```bash
unzip VulnScannerX-linux-amd64.zip
chmod +x VulnScannerX-linux-amd64
./VulnScannerX-linux-amd64 version
```

Windows 解压后可直接运行：

```powershell
.\VulnScannerX-windows-amd64.exe version
```

### 版本号与构建信息

VulnScannerX 在运行时可查看版本、提交号和构建时间：

```bash
./VulnScannerX-linux-amd64 version
```

输出示例：

```text
VulnScannerX 版本信息:
  版本: v3.1.4
  提交: 1a2b3c4
  构建时间: 2026-01-27T12:34:56Z
```

------

## 🚀 使用示例

### 1. Web GUI 模式（推荐）

启动 Web 服务，访问 `http://127.0.0.1:8080` 进行可视化操作：

```bash
./VulnScannerX-linux-amd64 web --listen :8080 --web-user admin --web-password admin
```

- 在「新建任务」时可勾选 **启用敏感信息收集**：
  - 对 URL / IP 资产做浅层爬取（默认深度 1，`light`），识别云密钥、Webhook、Token 等敏感信息；
  - 结果以 Info 级“漏洞详情”落入 Web 数据库，方便统一检索与追溯。

- 在「系统设置」中可配置 **方糖 SendKey**：
  - 在 `https://sct.ftqq.com/` 申请 SendKey；
  - 在 Web 界面「系统设置 → 集成配置」中填入 SendKey 后保存；
  - 后端会同时写入环境变量 `VULNSCANNERX_FANGTANG_KEY`，用于后续任务结果推送。

- 在 Web 界面「系统设置」保存后，会自动在**运行路径**生成本地配置文件：
  - 文件名：`vulnscannerx_web_settings.json`
  - 重启 `web` 模式时，会优先加载该文件并回灌到系统设置项。

- 在 Web 界面「系统设置 → 扫描范围控制」可配置扫描护栏：
  - `scan_allow_cidrs` / `scan_deny_cidrs`：CIDR 网段白名单与黑名单；
  - `scan_allow_hosts` / `scan_deny_hosts`：主机名或通配规则（如 `*.example.com`）；
  - `scan_allow_ports` / `scan_deny_ports`：端口列表或区间（如 `80,443,10000-10100`）。

- 当通过 Web 控制端创建 Web/URL 任务并由 Worker 执行完成后：
  - 若配置了方糖 SendKey 且本次扫描产生漏洞/敏感信息记录，则会自动推送一条「任务完成」摘要消息，其中包含：任务 ID / 目标、资产数量、漏洞数量以及部分示例资产和漏洞/敏感信息；
  - 若本次任务中存在高危/严重级别漏洞（`severity=critical|high` 或包含“高危”），会额外再推送一条「高危漏洞告警」消息，用于对接值班通知或监控告警通道。

### 2. 分布式 Worker 模式（水平扩展 URL/Web 扫描）

在 Web 控制端上创建 URL/Web 任务后，可通过 Worker 节点进行分布式执行，并可选用腾讯云函数（SCF）作为 HTTP 代理出口：

```bash
# 启动 Web 控制端
./VulnScannerX-linux-amd64 web --listen :8080 --web-user admin --web-password 'Str0ngPass!'
# 或通过参数同时指定 Agent Worker 通道加密密钥（任务/规则/控制）
./VulnScannerX-linux-amd64 web --listen :8080 --web-user admin --web-password 'Str0ngPass!' --agent-channel-key 'ChangeMeToStrongSecret'

# 在多台 Worker 主机上执行（推荐使用环境变量传递密码）
export VULNSCANNERX_WEB_PASSWORD='Str0ngPass!'
# 可选：开启 Agent Worker 全通道端到端加密（任务下发/结果回传/规则同步/控制）
export VULNSCANNERX_AGENT_CHANNEL_KEY='ChangeMeToStrongSecret'
./VulnScannerX-linux-amd64 worker --server http://10.0.0.10:8080 --user admin --interval 5
# 或通过参数指定（效果同上）
./VulnScannerX-linux-amd64 worker --server http://10.0.0.10:8080 --user admin --interval 5 --agent-channel-key 'ChangeMeToStrongSecret'
```

- Worker 启动后会自动登录控制端 `/api/login`，并定期上报 CPU/内存/负载等指标；
- 控制端按实时容量将 URL/Web 任务分发给在线 Worker，并在 Web 界面展示任务进度与节点状态；
- Worker 会周期校验主节点外置规则哈希，变更时自动拉取快照、覆盖本地 `plugins/pocs` 与 `plugins/fingerprints` 并热重载；
- 当 Web 与 Worker 同时配置 `VULNSCANNERX_AGENT_CHANNEL_KEY`（或使用 `--agent-channel-key` 参数）时，Agent Worker 通道（任务下发、结果回传、规则同步、Shell/文件管理控制）会使用 `AES-256-GCM` 进行应用层加密；
- 支持与云函数代理（腾讯云函数 SCF）联动，将指纹识别与 POC HTTP 请求经由云函数中转，以进一步降低源站暴露风险（需在「系统设置 → 云函数代理」中配置 SCF 触发 URL 与 Token，或通过环境变量 `VULNSCANNERX_SCF_PROXY_URL`、`VULNSCANNERX_SCF_PROXY_TOKEN` 进行配置）。

### 3. 云函数代理（腾讯云函数 SCF）示例

1. 在腾讯云函数创建一个 HTTP 触发的函数，用于转发外部 HTTP 请求到目标资产；
2. 在 VulnScannerX Web 界面「系统设置 → 云函数代理」中填入：
   - SCF 触发器 URL（如 `https://service-xxxxx.ap-guangzhou.apigw.tencentcs.com/release/scf-proxy`）；
   - 可选的访问令牌 Token（在 SCF 侧通过 `X-SCF-Token` 头校验）；
3. 或者直接通过环境变量配置后再启动 Web/Worker：

```bash
export VULNSCANNERX_SCF_PROXY_URL="https://service-xxxxx.ap-guangzhou.apigw.tencentcs.com/release/scf-proxy"
export VULNSCANNERX_SCF_PROXY_TOKEN="YourTokenHere"
./VulnScannerX-linux-amd64 web --listen :8080 --web-user admin --web-password 'Str0ngPass!'
```

配置完成后，当启用指纹识别或 POC 模块且打开 SCF 代理开关时，对目标资产的 HTTP 探测和漏洞验证会经由腾讯云函数转发，便于隐藏真实出口和对流量进行统一控制。

---

## 🔑 敏感信息收集模块概览

- **启用方式**：
  - Web GUI：在新建任务时勾选「启用敏感信息收集」。
  - 测绘联动：在 Web 新建任务中开启「搜索引擎联动」后，可对检索出的 Web 资产做二次爬取与匹配。

- **扫描范围与行为**：
  - 仅对 HTTP/HTTPS 资产进行敏感信息采集；
  - 默认爬取深度 `MaxDepth=1`（`light`）；`standard/deep` 模式为 `MaxDepth=2`；
  - 复用 OPSEC 行为控制（任务 OPSEC 模式 `normal|stealth`），在隐蔽模式下自动降低并发与请求频率。

- **典型命中类型**（非完整列表）：
  - 云访问密钥：AWS、Aliyun、Tencent、七牛等 AK/SK；
  - 各类 Webhook：钉钉、飞书等通知机器人地址；
  - Token/凭证：JWT、Bearer/Basic Auth、敏感 Cookie/Session、API Key；
  - 数据库与中间件：JDBC/Redis/Mongo/Postgres URI；
  - 其他：内部 IP、邮箱、手机号、身份证号等信息资产。

- **结果呈现**：
  - Web GUI：以 Info 级“漏洞详情”存储在 Web 数据库，可按项目/任务维度检索；
  - Web 资产导出（JSON/CSV）：导出资产字段（主机、端口、协议、指纹、漏洞摘要等）；敏感信息明细以 `kind=sensitive` 记录在漏洞详情中。

---

## 🪪 License 校验机制（实际行为）

- **校验链路**：
  1. 本地 Ed25519 签名校验；
  2. 解析并校验 `expire` 到期时间；
  3. 在线授权校验（`/verify`）成功则使用在线等级并写入本地缓存；
  4. 在线失败时离线回退：`payload tier/code` -> 本地缓存 tier -> `free`。

- **Web 界面状态**（系统设置 -> License）：
  - `online`：在线校验成功；
  - `offline-cache`：离线使用本地缓存等级；
  - `offline-payload`：离线使用 license 载荷中的等级；
  - `offline-free`：离线回退到 `free`；
  - `local-signature`：仅本地签名/日期可解析（通常用于已过期展示）。

- **free 配额限制**（默认）：
  - 最大项目数：`1`
  - 24 小时最大任务数：`20`
  - 最大并发任务数：`1`

------

## 📁 发布内容

```text
.
├── README.md
├── SHA256SUMS.txt
├── VulnScannerX-darwin-amd64.zip
├── VulnScannerX-darwin-arm64.zip
├── VulnScannerX-linux-386.zip
├── VulnScannerX-linux-amd64.zip
├── VulnScannerX-linux-arm64.zip
├── VulnScannerX-windows-386.zip
└── VulnScannerX-windows-amd64.zip
```
