# ManScan Templates

`manscan-templates` 是一个以 `YAML` 为核心的安全检测模板仓库，用于维护漏洞检测、错误配置检查、暴露面发现和扫描工作流规则。仓库目录结构、模板语法和校验方式整体沿用 `nuclei` 模板生态，同时适配团队内部的 `ManScan` 使用场景。
如果你需要新增模板、修正匹配逻辑，或按场景组织批量扫描，这个仓库就是日常维护入口。

## 📌 你可以从这里开始

- 想快速跑一个模板：直接看下方 `🚀 快速开始`
- 想知道模板放哪里：看 `🧱 目录结构`
- 想提交或修改模板：看 `🛠️ 开发规范`
- 想排查常见报错：看 `❓ 常见问题`

## 📦 项目简介

仓库按协议、场景和能力维度拆分模板，覆盖 `http`、`dns`、`ssl`、`network`、`javascript`、`dast`、`cloud` 等多个方向。除了单条检测模板外，还维护了 `workflows/` 下的组合扫描工作流，以及 `profiles/` 下的场景化扫描配置。
它更像“规则库”而不是应用服务：核心工作是补充覆盖、减少误报、统一模板质量，并确保新增内容能通过自动化校验。

## 🚀 快速开始

### 前置条件

- 已安装 `nuclei`
- 本地具备 `python3`、`git`

> 说明：仓库现有 CI、配置注释和模板文档均以 `nuclei` 为基线。
> 如果你的环境通过 `ManScan` 封装执行这些模板，可将下方命令中的 `nuclei` 按需替换为 `manscan`。

### 获取仓库

```bash
git clone <your-repo-url>
cd manscan-templates
```

### 运行单个模板

```bash
nuclei -t http/cves/2020/CVE-2020-36884.yaml -u https://example.com
```

适合快速验证某条模板是否生效，或在修改后做定向回归。

### 运行工作流

```bash
nuclei -w workflows/wordpress-workflow.yaml -u https://example.com
```

`workflows/` 中的模板会先识别目标技术，再串联执行对应子模板，适合按产品或场景批量扫描。

### 使用推荐扫描配置

```bash
nuclei -profile recommended -u https://example.com
```

仓库中的 `profiles/recommended.yml` 提供了一个更聚焦的扫描范围，适合作为常规入口。
如果你的 `nuclei` 版本或使用方式要求显式指定文件路径，也可以使用：

```bash
nuclei -config profiles/recommended.yml -u https://example.com
```

### 校验模板是否合法

```bash
nuclei -duc -validate -lfa -ud "$(pwd)" -w workflows/ -et .github/ -ept code -et helpers/payloads/
```

这是仓库 CI 正在使用的核心校验命令。新增或修改模板后，建议至少执行一次，确认语法、引用路径和工作流结构都没有问题。

## 🧰 技术栈

- `Nuclei Template YAML`：仓库的核心资产，用于定义检测逻辑、风险特征、工作流和场景配置。
- `Nuclei`：模板解释与执行引擎，也是仓库 CI 和辅助文档的事实标准。
- `ManScan`：团队内部使用语境中的扫描入口，可在兼容场景下复用本仓库模板。
- `GitHub Actions`：负责执行 `yamllint`、模板合法性校验、弱匹配器检查，以及部分衍生文件生成。
- `Python / Go` 脚本：用于 README、统计文件和 `cves.json` 等衍生内容的生成与更新。

## 🧱 目录结构

```text
.
|-- http/          # HTTP 相关模板，含 CVE、默认口令、指纹、暴露面等
|-- network/       # TCP 等网络协议模板
|-- dns/           # DNS 枚举、接管检测、解析配置检查
|-- ssl/           # TLS 与证书相关检查
|-- javascript/    # JavaScript 模板与部分客户端逻辑检测
|-- dast/          # 动态应用安全测试模板
|-- cloud/         # 云配置与 Kubernetes 相关规则
|-- code/          # 本地代码或系统侧检测规则
|-- workflows/     # 按产品或场景组合多个模板的工作流
|-- profiles/      # 推荐扫描配置和专题扫描配置
|-- helpers/       # 辅助 payload、swagger 文件等资源
|-- .github/       # CI、自动化脚本、Issue 模板
|-- .yamllint
|-- .nuclei-ignore # ManScan 默认忽略列表
```

重点目录说明：

- `http/`、`network/`、`dns/`、`ssl/` 是最常见的模板落点，新增模板时优先按协议或检测方式归类。
- `workflows/` 适合把多个模板串成一条扫描链，避免把重复逻辑复制到单个模板里。
- `profiles/` 用于定义“推荐扫描”“云配置”“OSINT”等批量执行范围。
- `.nuclei-ignore` 已明确标注为自动维护文件，更适合视为默认排除策略，而不是人工长期编辑入口。

## 🛠️ 开发规范

### 模板编写

- 新模板提交前，先检查仓库中是否已有同类规则，避免重复建设。
- `id` 应简短且可读，通常使用产品名、漏洞类型或 `CVE` 编号。
- `info` 中至少补全 `name`、`author`、`severity`、`description`、`reference`、`tags`。
- 尽量使用多个强匹配条件组合，不要只依赖过短关键词或通用报错文本。
- 工作流模板放在 `workflows/`，单条检测模板放在对应协议目录，不要混放。

### 本地检查

- 修改模板后建议执行仓库 CI 同款校验命令

```bash
nuclei -duc -validate -lfa -ud "$(pwd)" -w workflows/ -et .github/ -ept code -et helpers/payloads/
```

### CI 规则

- `.github/workflows/tests.yml` 会在 PR 中执行 `yamllint`
- 同一工作流还会使用 `nuclei` 做模板合法性校验
- 新增或修改的模板还会经过弱匹配器检查；如果在蜜罐目标上命中，PR 会被标记为潜在误报

### 协作建议

- 当前仓库未发现独立的 commit message 规范工具，可优先采用清晰的英文动作描述，例如 `add`、`fix`、`update`
- 如需了解完整贡献和模板审查标准，可进一步阅读：
  - [CONTRIBUTING.md](CONTRIBUTING.md)
  - [TEMPLATE-CREATION-GUIDE.md](TEMPLATE-CREATION-GUIDE.md)
  - [TEMPLATE-REVIEW-GUIDE.md](TEMPLATE-REVIEW-GUIDE.md)

## ❓ 常见问题

### 1. `nuclei -validate` 失败

常见原因：

- YAML 缩进错误
- `workflow` 或模板路径写错
- 模板字段不符合当前 `nuclei` 语法要求

解决方式：

- 先执行仓库标准校验命令定位报错文件
- 参考同目录下已通过校验的模板修正字段格式

### 2. 模板能跑，但 PR 被提示 weak matcher

常见原因：

- 匹配词过短、过泛
- 只校验状态码或单个通用报错字符串
- 缺少漏洞特征、版本特征或交互式证明

解决方式：

- 增加多个匹配条件，并配合 `matchers-condition` 使用
- 优先加入版本信息、产品标识、漏洞特征字符串
- 对高风险模板尽量补充可验证但低副作用的 POC 逻辑

### 3. 不确定模板该放在哪个目录

建议优先按“协议或执行方式”归类：

- Web 请求类优先放 `http/`
- DNS 查询类放 `dns/`
- 证书与 TLS 检查放 `ssl/`
- 需要串联多个模板时放 `workflows/`

如果同一技术已经存在专属目录，尽量沿用现有结构，减少后续整理成本。

### 4. `.nuclei-ignore` 里的内容想改怎么办

这个文件头部已说明会被自动更新，不适合作为人工长期维护入口。
如果只是本地扫描想额外排除某些标签或模板，建议把规则写到自己的扫描配置中，而不是直接修改仓库默认忽略列表。

## 📚 相关文档

- [CONTRIBUTING.md](CONTRIBUTING.md)
- [TEMPLATE-CREATION-GUIDE.md](TEMPLATE-CREATION-GUIDE.md)
- [TEMPLATE-REVIEW-GUIDE.md](TEMPLATE-REVIEW-GUIDE.md)
- [profiles/README.md](profiles/README.md)

## 📝 待补充

当前仓库能够确认模板维护、校验和贡献流程，但根目录下没有看到统一的发布策略、本地开发脚本约定或团队内同步方式说明。
如果后续需要继续完善 README，建议补一节“ManScan 如何消费本仓库模板”，专门说明私有配置、同步流程和版本管理约定。
