## profiles 简介

`profiles` 目录存放的是 ManScan 的扫描配置（Profile），并不是单条漏洞模板。  
这些配置文件会按照不同的扫描目标、协议类型、使用场景或安全主题，对模板进行筛选、组合或排除，方便在实际工作中直接调用。

相比直接全量加载模板，使用 Profile 可以更有针对性地执行扫描，减少无关结果，并提升扫描效率。

## 适用场景

这个目录中的配置主要面向以下场景：

- 日常通用安全扫描
- 渗透测试与漏洞排查
- OSINT 与资产发现
- 默认口令与弱口令检测
- 错误配置与安全基线检查
- 本地提权与主机审计
- 云环境配置审计
- Kubernetes 集群安全检查
- WordPress 专项检测
- 子域名接管检测
- 已知高风险漏洞与 KEV 漏洞排查

## 常见配置说明

以下是 `profiles` 目录中较常见配置文件的用途说明：

- `recommended.yml`
    - 推荐的通用网络扫描配置（不含本地安全扫描）。
    - 适合大多数日常扫描场景，通常会平衡覆盖范围、误报控制和执行效率。

- `all.yml`
    - 全量网络扫描配置（不含本地安全扫描）。
    - 会按照协议类型加载多类模板，例如 `http`、`tcp`、`javascript`、`dns`、`ssl` 等，适合需要尽可能全面覆盖的场景。

- `pentest.yml`
    - 面向渗透测试的扫描配置，不含 dos 攻击，fuzz 攻击，开源情报搜集。
    - 更偏向攻击面发现、漏洞验证和常见高价值风险识别。

- `osint.yml`
    - 面向信息收集与公开情报的扫描配置。
    - 适合做目标资产外部暴露面和可识别信息的梳理。

- `cves.yml`
    - 面向 CVE 漏洞检测的配置。
    - 适合快速聚焦已知编号漏洞。

- `kev.yml`
    - 面向已知被利用漏洞（Known Exploited Vulnerabilities）的配置。
    - 更适合优先排查现实攻击中更活跃的高风险漏洞。

- `misconfigurations.yml`
    - 面向错误配置与不安全部署的检测配置。
    - 适合发现开放服务、危险设置、暴露面异常等问题。

- `default-login.yml`
    - 面向默认口令、弱口令登录场景的配置。
    - 常用于排查设备、面板、后台系统是否存在出厂凭据或常见默认账户密码。

- `privilege-escalation.yml`
    - 面向本地提权检测的配置。
    - 通常结合 `code` 类模板，对主机环境中的危险权限、错误配置和提权条件进行检查。

- `windows-audit.yml`
    - 面向 Windows 主机安全审计的配置。
    - 适合检查系统配置、策略、安全基线和可疑设置。

- `cloud.yml`
    - 面向云环境的综合检测配置。
    - 一般用于统一检查常见云平台相关的风险暴露。

- `aws-cloud-config.yml`
- `azure-cloud-config.yml`
- `gcp-cloud-config.yml`
- `alibaba-cloud-config.yml`
    - 这几类配置分别面向不同云平台的配置审计。
    - 适合按云厂商分别排查控制台、对象存储、访问控制、服务配置等安全问题。

- `k8s-cluster-security.yml`
    - 面向 Kubernetes 集群安全检查。
    - 适合发现 API 暴露、权限配置不当、组件暴露和集群相关错误配置。

- `wordpress.yml`
    - 面向 WordPress 站点的专项检测配置。
    - 常用于检查插件、主题、已知漏洞和暴露接口。

- `subdomain-takeovers.yml`
    - 面向子域名接管检测的配置。
    - 用于排查 DNS 记录残留、第三方服务解绑不完整等问题。

- `compliance.yml`
    - 面向合规和安全基线检查的配置。
    - 适合在制度化、标准化检查场景中使用。

- `ai.yml`
    - 面向 AI 相关目标或场景的配置。
    - 适合对 AI 服务、接口或相关暴露面进行专项检测。

## 使用方式

可以直接通过 `-config` 或 `-profile` 的方式加载这些配置文件。

### 使用推荐配置

```bash
manscan -config ~/manscan-templates/profiles/recommended.yml
```

或者：

```bash
manscan -profile recommended -u https://example.com
```

### 使用指定场景配置

例如执行本地提权相关检查：

```bash
manscan -config ~/manscan-templates/profiles/privilege-escalation.yml
```

例如执行 OSINT 相关扫描：

```bash
manscan -config ~/manscan-templates/profiles/osint.yml
```

例如执行 WordPress 专项检测：

```bash
manscan -config ~/manscan-templates/profiles/wordpress.yml
```

## 自定义配置

如果现有 Profile 无法完全满足你的场景，可以参考 `recommended.yml` 或其他配置文件的结构，自行创建自定义配置文件，对以下内容进行筛选：

- 扫描协议类型
- 严重等级
- 标签
- 模板 ID
- 排除项

自定义完成后，可以通过以下方式运行：

```bash
manscan -config your-custom-config.yml
```

## 说明

- `profiles` 目录中的文件本质上是“扫描策略”或“模板组合方案”，不是单条漏洞模板。
- 这些配置通常通过 `severity`、`type`、`tags`、`include/exclude`、`id` 等条件来组织模板。
- 在大规模扫描时，优先使用合适的 Profile，通常比直接全量运行模板更实用。
