# ManScan Templates

`manscan-templates` 是一个以 `YAML` 模板为核心的安全检测规则仓库，用于维护漏洞检测、配置错误检查、暴露面发现、情报收集与扫描工作流。仓库整体沿用 `nuclei` 模板生态的目录组织和语法约定，同时适配当前团队使用的 `ManScan` 执行方式。

如果你要做这些事情，这个仓库就是日常入口：

- 新增或修复漏洞模板
- 调整匹配逻辑，降低误报
- 按产品、协议或场景组织批量扫描
- 维护推荐扫描配置、工作流和辅助 payload

## 📌 阅读导航

- 想尽快跑起来：看 [🚀 快速开始](#-快速开始)
- 想了解仓库里都放了什么：看 [🧱 目录结构](#-目录结构)
- 想知道模板怎么提交更稳：看 [🛠️ 开发规范](#-开发规范)
- 想排查常见问题：看 [❓ 常见问题](#-常见问题)

## 📦 项目简介

这个仓库不是传统意义上的应用服务，而是一套可长期维护的扫描规则库。当前快照下，仓库中包含约 `13,000+` 个 `YAML` 配置文件，其中包括 `206` 个工作流和 `20` 个扫描配置文件，覆盖 `http`、`network`、`dns`、`ssl`、`javascript`、`dast`、`cloud`、`code`、`file` 等多个方向。

仓库的核心目标不是“尽量多扫”，而是围绕以下几个方向持续演进：

- 让模板具备稳定、可复核的检测证据
- 让目录结构、字段风格和命名保持一致
- 让工作流和配置文件能复用已有模板，而不是重复造轮子
- 让新增内容在批量扫描场景下更可控，减少弱匹配和噪声结果

## 🚀 快速开始

### 前置条件

推荐准备以下环境：

- `git`
- `manscan` 可执行文件
- 可选：如果你就在当前仓库环境中操作，也可以直接使用根目录里的 `./manscan`

### 克隆仓库

```bash
git clone <your-repo-url>
cd manscan-templates
```

### 运行单个模板

```bash
manscan -t http/cves/2020/CVE-2020-36884.yaml -u https://example.com
```

适合用来做定向验证、模板调试或修改后的最小回归。

### 运行工作流

```bash
manscan -w workflows/wordpress-workflow.yaml -u https://example.com
```

工作流会先识别目标特征，再串联执行相关子模板，适合按产品或场景做批量扫描。

### 使用扫描配置

推荐从 `profiles/recommended.yml` 开始：

```bash
manscan -config profiles/recommended.yml -u https://example.com
```

这个配置默认包含：

- `critical` 到 `low` 级别的常见模板
- `dns`、`ssl`、`tcp`、`http`、`javascript` 等主要协议类型
- 一批默认排除的高噪声模板和弱匹配模板

如果要做专题扫描，也可以直接切换到其他配置，例如：

```bash
manscan -config profiles/osint.yml -u https://example.com
manscan -config profiles/wordpress.yml -u https://example.com
```

### 校验模板语法

修改模板后，至少建议做一次本地校验：

```bash
manscan -validate -t http/cves/2020/CVE-2020-36884.yaml
```

如果你需要按整个仓库做更完整的验证，可以使用当前 README 原先沿用的仓库级校验命令：

```bash
manscan -duc -validate -lfa -ud "$(pwd)" -w workflows/ -ept code -et helpers/payloads/
```

这条命令适合在大批量修改后做整体自检，用来提前发现模板语法、工作流引用或本地 payload 路径问题。

## 🧱 目录结构

下面是最值得先理解的目录：

```text
.
|-- http/         # HTTP 类模板，包含 CVE、暴露面、默认口令、配置错误等
|-- network/      # TCP 与其他网络协议类模板
|-- dns/          # DNS 枚举、解析检查、接管类模板
|-- ssl/          # TLS、证书与加密配置相关模板
|-- javascript/   # JavaScript 运行时、客户端能力与枚举模板
|-- dast/         # 动态应用安全测试模板
|-- cloud/        # 云平台、Kubernetes 与云配置类模板
|-- code/         # 本地代码、系统或离线检测类模板
|-- file/         # 文件内容、源码痕迹、敏感信息类模板
|-- workflows/    # 多模板组合扫描工作流
|-- profiles/     # 扫描配置文件
|-- helpers/      # payload、swagger 文件和其他辅助资源
|-- CODE_STYLE.md
|-- SYNTAX-REFERENCE.md
|-- AGENTS.md
|-- .nuclei-ignore
```

目录使用建议：

- 新增单条 Web 检测模板时，优先放到 `http/` 下对应子目录。
- 需要按产品串联多个模板时，优先放到 `workflows/`，不要把流程逻辑硬塞进单个模板。
- 需要定义一组推荐扫描范围时，放到 `profiles/`。
- 依赖本地辅助文件的模板，优先复用 `helpers/payloads/` 中已有资源。

## 🛠️ 开发规范

### 模板编写入口

本仓库已经明确提供了三份重要文档，建议按下面顺序阅读：

1. [AGENTS.md](AGENTS.md)
2. [CODE_STYLE.md](CODE_STYLE.md)
3. [SYNTAX-REFERENCE.md](SYNTAX-REFERENCE.md)

其中：

- `AGENTS.md` 定义了当前仓库中处理模板时的强制规则
- `CODE_STYLE.md` 更聚焦模板结构、命名、字段顺序、误报控制与自检要求
- `SYNTAX-REFERENCE.md` 适合在协议块、匹配器、语法细节上快速查阅

### 日常修改建议

- 先确认仓库里是否已有同类模板，避免重复建设。
- 修改前先理解目标模板的用途、验证方式和影响范围，不要机械改写。
- 新建模板时，优先证明漏洞，再设计匹配器。
- 优先使用更强的多条件组合匹配，避免只依赖短关键词、单一状态码或泛化报错。
- 保持原有结构、缩进、字段顺序和语义风格稳定，尤其是在修补已有模板时。

### 本地自检建议

推荐至少做这三类检查：

1. 单模板语法校验

```bash
manscan -validate -t path/to/template.yaml
```

2. 工作流或目录级回归

```bash
manscan -w workflows/wordpress-workflow.yaml -u https://example.com
```

3. 全仓库结构性校验

```bash
manscan -duc -validate -lfa -ud "$(pwd)" -w workflows/ -ept code -et helpers/payloads/
```

### 关于忽略列表

根目录的 [`.nuclei-ignore`](.nuclei-ignore) 已明确标注为自动维护文件。当前内容主要用于：

- 默认忽略 `dos`、`local`、`fuzz`、`bruteforce` 等高风险或高噪声标签
- 排除一批弱匹配或已知容易误报的模板文件

如果只是你的本地扫描需要额外排除内容，更建议把规则写到自己的配置文件，而不是直接改仓库默认忽略列表。

### 当前仓库可确认的协作现状

- 已有明确的模板编写与仓库协作规范文档
- 当前仓库快照下未发现 `.github/` 目录或可直接引用的 CI 工作流文件
- 当前也未发现独立的 `CONTRIBUTING.md`、`TEMPLATE-CREATION-GUIDE.md`、`TEMPLATE-REVIEW-GUIDE.md`

因此，提交前更应依赖本地校验和现有规范文档，而不是假设仓库会自动帮你兜底。

## 🧭 常用工作流与配置

如果你是第一次接触这个仓库，下面几类文件最值得先看：

- `profiles/recommended.yml`
  适合做常规入口扫描，覆盖面和噪声控制相对平衡。
- `profiles/osint.yml`
  适合情报收集、钓鱼、暴露面与相关枚举场景。
- `profiles/wordpress.yml`
  适合 WordPress 相关目标的聚焦扫描。
- `workflows/wordpress-workflow.yaml`
  适合理解“先识别目标，再串联漏洞模板”的组织方式。

补充阅读：

- [profiles/README.md](profiles/README.md)

## ❓ 常见问题

### `manscan -validate` 失败怎么办

常见现象：

- 报 YAML 语法错误
- 报字段不合法
- 报模板路径或工作流引用不存在

排查建议：

- 先对单个模板执行 `-validate`
- 再检查缩进、字段拼写、协议块层级是否符合当前语法
- 如果模板依赖本地文件，确认引用路径是否真实存在

### 模板能运行，但结果明显误报

常见原因：

- 只依赖状态码
- 匹配词过短、过泛
- 没有把产品指纹和漏洞证据区分开

解决思路：

- 增加多个独立特征，并配合 `matchers-condition`
- 优先加入版本、组件名称、特定报错、响应结构等更强证据
- 对高风险漏洞尽量补充更可复核的验证逻辑

### 不确定模板该放到哪个目录

可按下面思路快速判断：

- Web 请求类：`http/`
- DNS 查询类：`dns/`
- TLS / 证书类：`ssl/`
- 本地文件或源码类：`file/` 或 `code/`
- 需要组合多个模板的流程类：`workflows/`

如果已有同产品或同技术专属目录，优先沿用现有结构。

### 想直接修改 `.nuclei-ignore`

不推荐把它当成常规编辑入口。这个文件当前就是默认排除策略的载体，而且文件头已经声明会被自动更新。更稳妥的做法是把你的本地差异化排除逻辑放进自定义 profile 或扫描命令参数中。


## 📚 相关文档

- [AGENTS.md](AGENTS.md)
- [CODE_STYLE.md](CODE_STYLE.md)
- [SYNTAX-REFERENCE.md](SYNTAX-REFERENCE.md)
- [profiles/README.md](profiles/README.md)
- [LICENSE.md](LICENSE.md)
