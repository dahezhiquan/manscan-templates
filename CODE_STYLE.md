# 🧩 漏洞模板编写规范

本文档用于统一本仓库漏洞模板的编写风格、字段约定、语法使用、自检方式与评审标准。

> 🎯 这份文档既是模板编写规范，也是提交前自检手册。
> 适合在“新建模板前”“提交 PR 前”“Review 他人模板时”反复对照使用。

## 📌 快速导航

1. 基本原则：先理解漏洞，再设计模板
2. 命名与目录：`id`、文件名、存放路径怎么统一
3. 元数据规范：`info`、`reference`、`metadata`、`tags` 怎么写
4. 语法与协议：`http`、`code`、`network`、`dns` 等协议块如何组织
5. 误报控制：`matchers`、`extractors`、`payloads` 怎样写更稳
6. 提交与自检：提交前该检查什么，哪些写法最容易被打回

**适用范围：**

- `http`、`headless`、`javascript`、`network`、`dns`、`ssl`、`code`、`cloud`、`dast`、`file` 等目录下的模板
- 新增模板、修复模板、优化匹配器、补全元数据、降低误报的修改

**本文档的目标不是只告诉你“模板能跑就行”，而是帮助你写出：**

- 可读性高
- 误报率低
- 证据充分
- 命名统一
- 易于 Review 和长期维护

## ✅ 1. 编写模板的基本原则

写漏洞模板时，优先遵守以下五条原则：

1. 先理解漏洞，再写模板。
2. 先证明漏洞，再设计匹配器。
3. 先控制误报，再追求覆盖面。
4. 先保持结构统一，再添加高级语法。
5. 先补齐元数据，再提交 PR。

一句话概括：模板不是“猜测目标可能有漏洞”，而是“通过可重复验证的请求和响应，尽可能准确地证明目标存在某个问题”。

> 💡 记忆口诀：先理解、再验证、再收敛、再提交。

## 🔍 2. 编写前必须确认的事项

在开始写模板前，至少要确认下面这些信息：

- 漏洞类型：例如 `rce`、`sqli`、`xss`、`ssrf`、`lfi`、`auth-bypass`、`misconfig`
- 影响范围：受影响的产品、组件、路径、接口、版本
- 利用方式：请求方法、参数位置、特殊 Header、认证条件、回显方式
- 唯一特征：能证明漏洞存在的响应内容、状态码、报错、回连、时间差、响应头、返回结构
- 误报场景：相似产品、已修复版本、默认错误页、蜜罐、通用中间件响应
- 验证方式：本地复现、漏洞环境、公开 POC、厂商公告、NVD、研究文章

如果这些信息没有搞清楚，不要急着动手写 YAML。

> ⚠️ 如果漏洞边界、验证方式、误报场景都还不清楚，越早写模板，后面返工通常越多。

## 🗂️ 3. 模板文件放置与命名规范

### 3.1 目录选择

模板应放在最贴近其协议或用途的目录中。例如：

- HTTP 漏洞放在 `http/` 或 `http/cves/<year>/`
- DAST 漏洞放在 `dast/`
- 代码类模板放在 `code/`
- 云配置检查模板放在 `cloud/`
- DNS 检测模板放在 `dns/`
- 网络协议扫描模板放在 `network/`

常见目录语义建议：

- `cves/`：CVE 漏洞
- `vulnerabilities/`：通用漏洞
- `misconfiguration/`：配置错误
- `exposures/`：敏感信息暴露
- `default-logins/`：默认口令
- `technologies/`：指纹识别
- `honeypot/`：蜜罐识别
- `enumeration/`：枚举与信息收集

### 3.2 文件名规范

文件名必须和 `id` 一致。

例如：

```yaml
id: nps-auth-bypass
```

文件名应为：

```text
nps-auth-bypass.yaml
```

对于 CVE 模板，优先使用：

```yaml
id: CVE-2024-12345
```

对应文件名：

```text
CVE-2024-12345.yaml
```

不要出现以下问题：

- 文件名和 `id` 不一致
- `id` 太长，像一句描述
- `id` 太泛，例如 `test-rce`、`app-bug`
- 同一个漏洞在仓库中重复命名

### 3.3 `id` 命名建议

推荐使用 2 到 4 个短词组成，具备清晰语义。

推荐：

- `nps-auth-bypass`
- `grafana-unauth-rce`
- `apache-struts-ognl-rce`

不推荐：

- `vulnerability-test`
- `panel-check`
- `sql-injection-detect-template`

## 🧱 4. 推荐的模板结构

大多数模板建议遵循如下结构：

```yaml
id: template-id

info:
  name: 产品名 - 漏洞类型
  author: your-handle
  severity: high
  description: |
    用简明文字说明该模板检测什么问题。
  reference:
    - https://example.com/advisory
  metadata:
    verified: true
  tags: vendor,product,vuln-type

http:
  - method: GET
    path:
      - "{{BaseURL}}/path"

    matchers-condition: and
    matchers:
      - type: status
        status:
          - 200

      - type: word
        part: body
        words:
          - "unique-indicator"
```

推荐的顶层顺序：

1. `id`
2. `info`
3. 其他顶层配置
4. 协议块，如 `http`、`network`、`dns`、`code`

常见的其他顶层配置包括：

- `variables`
- `flow`
- `self-contained`
- `stop-at-first-match`

## 🏷️ 5. `info` 字段编写规范

`info` 是模板的元信息区，必须清晰、完整、可读。

### 5.1 📝 `name`

漏洞或 CVE 模板建议命名为：

```text
<厂商> <产品> <版本范围> - <漏洞类型>
```

例如：

- `NPS - 认证绕过漏洞`
- `Apache Struts 2.x - 远程代码执行漏洞`
- `Adobe ColdFusion <= 8.0.1 - 跨站脚本攻击漏洞`

要求：

- 使用汉化的中文名称
- 使用正式产品名
- 漏洞类型写清楚，不要只写 `Vulnerability`
- 尽量避免全大写乱用
- 保持中文名称自然、简洁、语法正确

### 5.2 👤 `author`

填写模板编写者 ID；如果有明确的原始漏洞发现者，建议一并署名。

例如：

```yaml
author: your-handle,researcher-handle
```

建议：

- 多个作者用英文逗号分隔
- 不要使用中文逗号

### 5.3 🚨 `severity`

可用值通常为：

- `info`
- `low`
- `medium`
- `high`
- `critical`

建议结合 CVSS、利用门槛和实际影响综合判断：

- `critical`：远程代码执行、完全接管、无需认证
- `high`：高价值数据泄露、权限提升、认证绕过
- `medium`：有限信息泄露、具备前置条件的漏洞
- `low`：影响有限、验证意义更强于实际危害
- `info`：资产识别、行为识别、技术指纹

### 5.4 📖 `description`

用 1 到 3 句话写清楚：

- 漏洞本质
- 受影响对象
- 可能后果

推荐：

```yaml
description: |
  Jakarta Tomcat 3.1 和 3.0 信息泄露受影响版本中，存在信息泄露问题，远程攻击者可通过请求带 `.snp` 扩展的异常 URL 触发 Snoop Servlet 返回服务器环境信息。
```

避免：

- 只写“存在漏洞”
- 复制标题
- 没有具体影响描述
- 要求使用中文，避免使用英文

### 5.5 💥 `impact`

用 1 到 3 条写清楚：

- 漏洞造成的详细安全后果，需要结合漏洞实际危害编写，不要泛泛而谈
- 如果是多条，要分条展示，使用 md的 -


推荐：

```yaml
impact: |
  - 攻击者可在受害者浏览器中执行恶意脚本，窃取 Cookie、会话令牌或诱导执行未授权操作。
  - 若受害者为管理员，后台功能可能被间接接管并造成更大范围的数据与配置风险。
```

避免：

- 只写"危害严重"、"影响较大"等笼统描述
- 脱离漏洞实际利用场景，夸大或缩小危害
- 仅描述漏洞现象，不说明造成的后果
- 要求使用中文，避免使用英文
- 多条数据没有使用 md的 -



### 5.6 🛠️ `remediation`

给出修复方向，需要结合漏洞成因提供切实可行的修复方案，例如：

- 升级到官方修复版本
- 禁用危险功能
- 增加鉴权
- 限制来源
- 修复路径穿越输入点

如果是多条，要分条展示，使用 md的 -

推荐：

```yaml
impact: |
  - 优先按照厂商安全公告升级或修复受影响组件，避免继续暴露已知漏洞入口。
  - 对所有用户输入和输出进行严格过滤、转义和上下文编码，避免不可信内容直接进入 HTML、JS 或 URL。
  - 同时建议升级到修复版本，并结合 CSP、HttpOnly 和 SameSite 等策略降低利用风险。
```
避免：

- 只写"建议修复"、"及时打补丁"等空泛描述
- 修复方案与漏洞成因不匹配
- 仅给出单一修复方式，缺少纵深防御建议
- 不区分临时缓解措施与根本性修复
- 要求使用中文，避免使用英文
- 多条数据没有使用 md的 -



### 5.7 🔗 `reference`

必须尽量提供高质量参考链接，优先级建议如下：

1. 厂商公告
2. NVD / CVE 页面
3. 官方修复说明
4. 公开 POC 或技术分析文章
5. 产品主页或组件说明页

要求：

- 链接真实可访问
- 与模板用途直接相关
- 不要只放聚合转载页

### 5.8 🧭 `classification`

适用于 CVE 或可明确归类的漏洞，常见字段包括：

```yaml
classification:
  cvss-metrics: CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:H
  cvss-score: 9.8
  cve-id: CVE-2024-12345
  cwe-id: CWE-89
  cpe: cpe:2.3:a:vendor:product:*:*:*:*:*:*:*:*
```

建议：

- 能补则补，不要乱填
- `cve-id` 要和模板对应
- `cvss-score` 与 `severity` 不应明显冲突

### 5.9 📦 `metadata`

用于补充便于检索、验证和执行控制的信息，常见字段：

```yaml
metadata:
  verified: true
  max-request: 1
  shodan-query: 'http.title:"Product Login"'
  fofa-query: 'title="Product Login"'
  vendor: vendor-name
  product: product-name
```

说明：

- `verified: true` 只在你亲自验证或有明确调试证据时添加
- `max-request` 在仓库已有模板中可能出现，但通常属于执行统计或自动生成信息，新写模板时不要为了“补字段”而手工维护它
- `shodan-query` / `fofa-query` 用于资产发现，建议尽量补充
- `vendor` / `product` 有助于统一检索
- 若某字段无法确认，可以不加，不要编造

### 5.10 🏷️ `tags`

`tags` 是搜索、过滤和归类的重要字段。

基本要求：

- 使用英文小写短词
- 英文逗号分隔
- 不要包含空格
- 尽量覆盖“厂商 + 产品 + 漏洞类型 + 类别”

推荐包含：

- 厂商名，例如 `apache`、`atlassian`
- 产品名，例如 `struts`、`grafana`
- 漏洞类型，例如 `rce`、`xss`、`ssrf`、`sqli`
- 类别，例如 `cve`、`misconfig`、`auth`

特殊约定：

- CVE 模板必须带 `cve`
- CVE 模板建议带 `cve2024` 这种年份标签
- 认证类漏洞建议带 `auth`
- OAST / OOB 模板建议带 `oast` 或 `oob`
- 会修改文件、上传 webshell、改配置的模板建议带 `intrusive`
- DoS 模板建议带 `dos`
- 开源软件误配置可以考虑带 `oss`

例如：

```yaml
tags: cve,cve2024,apache,struts,rce
```

## ⚙️ 6. 常见顶层配置说明

### 6.1 🔁 `variables`

用于复用动态变量、降低重复、提高可读性。

例如：

```yaml
variables:
  cmd: "whoami"
  marker: "{{rand_base(8)}}"
```

适用场景：

- RCE 命令
- 唯一标记串
- 用户名密码占位
- 可复用路径片段

### 6.2 🔄 `flow`

用于多阶段协议流程，例如先执行 `code` 再执行 `javascript`，或多个阶段之间传递数据。

例如：

```yaml
flow: |
  code(1);
  for (let item of template.items) {
    set("item", item)
    javascript(1);
  }
```

建议：

- 仅在确有必要时使用
- 保持流程清晰，不要堆叠过多逻辑

### 6.3 📍 `self-contained`

适用于本地执行、无需外部网络目标的模板。

例如：

```yaml
self-contained: true
```

常见于：

- Windows / Linux / macOS 本地基线检查
- `code` 模板
- 云平台配置枚举脚本

## 🌐 7. 协议块编写规范

不同模板最终落在不同协议块中，例如：

- `http`
- `network`
- `dns`
- `ssl`
- `javascript`
- `code`

请遵循所选协议的官方语法，并尽量参考本仓库同目录下已有模板。

### 7.1 🌍 HTTP 模板

HTTP 是最常见的模板类型，常用字段包括：

```yaml
http:
  - method: GET
    path:
      - "{{BaseURL}}/admin"

    headers:
      User-Agent: Custom-Agent

    body: |
      key=value

    matchers:
      - type: status
        status:
          - 200
```

常见请求写法：

- `method + path`
- `raw`
- `payloads`
- `fuzzing`
- `headers`
- `body`
- `cookie-reuse` 或相关 Cookie 控制项

什么时候优先用 `raw`：

- 需要精确控制原始请求格式
- 需要特殊 Header 顺序
- 需要 multipart、边界、特殊编码
- 需要完整还原公开 POC

什么时候优先用结构化写法：

- 请求简单
- 便于维护
- 便于后续提取或插入变量

### 7.2 💻 Code 模板

适用于本地命令执行、配置审计、系统信息检查。

示例：

```yaml
self-contained: true

code:
  - pre-condition: |
      IsWindows();
    engine:
      - cmd
    args:
      - /c
    source: |
      net accounts
```

注意：

- 优先增加 `pre-condition`
- 命令尽量最小化，不做破坏性操作
- 输出匹配要稳定，避免依赖临时环境内容

### 7.3 🔌 Network / DNS / SSL 模板

这类模板通常更偏协议识别、服务检测、漏洞握手验证。

注意：

- 输入数据要准确
- 端口选择要合理
- `part` 往往不是 `body`，而是 `data`
- 协议字节流、十六进制、握手字段要与目标协议一致

### 7.4 🧪 DAST / Headless / JavaScript 模板

适用于更复杂的动态交互场景，例如：

- 前端交互
- 浏览器执行
- 参数污染
- OOB / OAST 验证

注意：

- 需要唯一标识串证明漏洞
- 不能只看页面出现“error”之类通用字样
- 有交互副作用时要谨慎，必要时标记 `intrusive`

## 📨 8. 请求设计规范

### 8.1 🧷 使用正确的变量

常见变量包括：

- `{{BaseURL}}`
- `{{Hostname}}`
- `{{Host}}`
- `{{randstr}}`
- `{{rand_base(8)}}`
- `{{interactsh-url}}`
- `{{unix_time()}}`

特殊场景建议：

- RCE / 注入类：优先使用 `{{cmd}}`、`{{payloads}}`、唯一标记串
- 认证类：使用 `{{username}}`、`{{password}}`
- Token 类：使用 `{{token}}`
- 文件上传类：使用 `{{randstr}}` 生成随机文件名

### 8.2 📉 请求数量要克制

模板请求数不是越多越好。

建议：

- 尽量使用最少请求完成验证
- 如果一个请求能完成，就不要拆成三个
- 会影响性能的模板要特别谨慎

### 8.3 ⚠️ 不要做无必要的破坏性操作

以下行为要谨慎：

- 上传 webshell
- 写入文件
- 修改配置
- 创建账号
- 删除资源

如果确实需要：

- 使用随机文件名
- 尽量可回滚
- 明确标记 `intrusive`
- 给出充分理由

## 🎯 9. Matchers 编写规范

`matchers` 是模板质量的核心。

### 9.1 基本原则

好的 matcher 应满足以下要求：

- 唯一
- 稳定
- 可复现
- 与漏洞直接相关
- 尽量不依赖用户输入回显

### 9.2 📍 强烈建议指定 `part`

常见值包括：

- `body`
- `header`
- `all_headers`
- `raw`
- `data`

建议显式指定，避免阅读和维护时产生歧义。

例如：

```yaml
matchers:
  - type: word
    part: body
    words:
      - '"VerifyKey":'
```

### 9.3 🧩 推荐至少两层验证

对 Web 模板，通常建议至少两个 matcher 组合，例如：

- 状态码 + 响应体特征
- 响应头 + 响应体特征
- 产品指纹 + 漏洞证明

例如：

```yaml
matchers-condition: and
matchers:
  - type: status
    status:
      - 200

  - type: word
    part: header
    words:
      - "application/json"

  - type: word
    part: body
    words:
      - '"VerifyKey":'
      - '"Password":'
    condition: and
```

### 9.4 🚫 避免弱匹配

以下内容通常过于宽泛，不适合作为漏洞证据：

- `error`
- `admin`
- `login`
- `success`
- `exception`

这些词在大量正常系统中都会出现，容易造成误报。

### 9.5 🚫 不要直接拿请求参数回显当漏洞证明

如果目标只是把你发送的参数原样返回，这通常不能证明漏洞成立。

错误示例：

```yaml
matchers:
  - type: word
    words:
      - "{{randstr}}"
```

只有在该随机串出现在“本不该出现的位置”，且能证明漏洞执行、注入、解析或存储成功时，这个匹配才有意义。

### 9.6 🪜 推荐的多层验证思路

建议按下面三层思路组织：

1. 识别产品
2. 限定漏洞上下文
3. 证明漏洞成立

例如：

```yaml
matchers-condition: and
matchers:
  - type: word
    part: body
    words:
      - "Grafana"

  - type: regex
    part: body
    regex:
      - 'version.*8\.[0-5]\.'

  - type: word
    part: body
    words:
      - "manscan-test-success"
```

### 9.7 🧰 常见 matcher 类型

- `word`
- `regex`
- `status`
- `binary`
- `dsl`

使用建议：

- 文本特征优先用 `word`
- 格式变化较大的内容用 `regex`
- 响应码判断用 `status`
- 复杂组合条件用 `dsl`

### 9.8 🧠 `dsl` 使用建议

`dsl` 适合表达多个条件组合，例如：

```yaml
matchers:
  - type: dsl
    dsl:
      - 'status_code == 200'
      - 'contains(body, "vulnerable_pattern")'
      - 'contains(header, "application/json")'
    condition: and
```

建议：

- 只在简单 matcher 不够时使用
- 表达式要易读
- 不要把所有逻辑都塞进一条超长 DSL

## 🧲 10. Extractors 编写规范

`extractors` 用于从响应中提取版本、路径、令牌、对象列表等信息。

示例：

```yaml
extractors:
  - type: regex
    name: version
    regex:
      - 'Version: ([0-9\\.]+)'
    group: 1
```

建议：

- 提取内容要与后续逻辑相关
- 命名清晰，例如 `version`、`items`、`token`
- 仅在需要时使用，避免无意义提取

## 🧪 11. Payloads 与 Fuzzing 使用建议

适用于参数注入、模糊测试、动态拼接场景。

例如：

```yaml
payloads:
  injection:
    - "<payload-1>"

fuzzing:
  - part: query
    type: postfix
    mode: single
    fuzz:
      - "{{injection}}"
```

建议：

- payload 命名要有语义
- 单个模板不要塞过多 payload
- 只保留与该漏洞强相关的 payload

## 🛡️ 12. 特定漏洞类型的额外要求

### 12.1 💣 RCE / 命令执行

建议：

- 使用 `{{cmd}}` 或等价变量
- 使用唯一标记串证明命令执行成功
- 尽量避免只匹配 `root`、`administrator` 这类宽泛输出

推荐思路：

- 输出唯一标记串
- 再匹配命令执行的上下文或预期结果

### 12.2 🗃️ SQL 注入

建议：

- 不要只依赖通用 SQL 报错
- 时间盲注要结合唯一请求上下文和持续时间判断
- 避免网络抖动导致误判

时间型 SQLi 额外要求：

- 使用唯一端点或唯一参数上下文
- 配合 DSL 判断响应时间

### 12.3 🌊 SSRF

建议：

- 增加响应型证据，而不是只看回连
- 考虑蜜罐和伪造响应问题
- 尽量结合目标产品特征与回显结果双重确认

### 12.4 🪞 XSS

建议：

- 使用唯一字符串作为 payload 一部分
- 证明 payload 被真实反射或执行，而不是普通文本回显
- 避免只因输入值出现在页面中就判断存在 XSS

### 12.5 📤 文件上传 / Webshell

只有在没有更安全替代方案时才这样做。

要求：

- 文件名使用随机值
- 明确标记 `intrusive`
- 验证上传结果要结合路径、返回内容、文件可访问性

### 12.6 📂 LFI / 任意文件读取

建议：

- Linux 与 Windows 路径可同时覆盖
- 匹配内容使用高可信文件特征

例如：

- Linux：`root:.*:0:0:`
- Windows：`\[fonts\]`

### 12.7 🔐 Auth / 默认口令 / 认证绕过

建议：

- 使用成功登录后的独占特征
- 不要只看 200 状态码
- 尽量匹配后台特有元素、会话特征、接口响应结构

## 🧯 13. 误报控制要求

低误报是高质量模板最核心的指标之一。

提交前至少要思考以下问题：

- 是否会命中同厂商其他产品？
- 是否会命中已修复版本？
- 是否会命中通用框架默认页？
- 是否会命中错误页、登录页、跳转页？
- 是否会被蜜罐故意诱导匹配？

建议测试对象包括：

- 已知漏洞版本
- 已修复版本
- 同类但不受影响的相似产品
- 常见 Web 框架默认页面

## ❌ 14. 常见反模式

以下写法在 Review 中通常会被要求修改：

- 只用一个宽泛 `word` matcher
- 只做版本识别，不证明漏洞存在
- 只看输入参数被回显
- `id`、文件名、目录不一致
- `tags` 缺少厂商或漏洞类别
- `reference` 质量低或与漏洞无关
- `severity` 与实际影响不匹配
- 乱加 `verified: true`
- 修改已有模板时顺手改动无关内容
- YAML 缩进混乱、存在尾随空格

## ✍️ 15. YAML 风格要求

### 15.1 📏 缩进

- 统一使用两个空格缩进
- 不要混用 Tab

### 15.2 📄 多行字符串

长描述、脚本、原始请求建议使用 `|`：

```yaml
description: |
  这里写多行说明。
```

### 15.3 📋 列表风格

保持统一：

```yaml
reference:
  - https://example.com/1
  - https://example.com/2
```

### 15.4 ␠️ 空格与标点

- 冒号后保留一个空格
- `tags` 不要写成带空格的 CSV
- 不要保留尾随空格

### 15.5 🤖 自动生成内容

部分模板文件末尾可能带有类似下面的内容：

```text
# digest: ...
```

这类内容通常是自动生成的摘要或校验信息。

建议：

- 不要手工编造或随意修改
- 如果仓库工具链会自动生成，保持其输出结果即可
- 如果当前修改未涉及该内容的生成流程，不要为了“格式统一”去单独改它

## 🧪 16. 验证与自检命令

提交前至少应执行：

```bash
manscan -validate -t path/to/template.yaml
```

建议进一步测试：

```bash
manscan -t path/to/template.yaml -target http://target.example -debug
```

如果条件允许，还应测试：

- 漏洞环境
- 已修复环境
- 相似但不受影响环境

如果仓库工作流或本地工具链支持，也建议运行 YAML lint 检查。

## ✅ 17. Review 自检清单

提交前，请至少逐项检查以下内容：

### 17.1 🧾 基础信息

- [ ] `id` 是否准确、简洁、唯一
- [ ] 文件名是否与 `id` 完全一致
- [ ] 目录是否放对
- [ ] `name` 是否符合产品名 + 漏洞类型格式
- [ ] `author` 是否规范

### 17.2 🏷️ 元数据

- [ ] `severity` 是否合理
- [ ] `description` 是否写清问题本质
- [ ] `reference` 是否真实且高质量
- [ ] `classification` 是否尽可能完整
- [ ] `metadata` 是否补充了 `verified`、`shodan-query`、`fofa-query` 等有价值信息
- [ ] `tags` 是否完整且无空格

### 17.3 🎯 检测逻辑

- [ ] 模板是否真的证明了漏洞存在
- [ ] 是否包含足够强的 matchers
- [ ] 是否明确指定了 `part`
- [ ] Web 模板是否至少有两层验证
- [ ] 是否避免使用弱匹配词
- [ ] 是否避免把请求回显误判为漏洞成功

### 17.4 🧯 风险控制

- [ ] 是否存在明显误报风险
- [ ] 是否测试过非漏洞环境
- [ ] 是否会修改目标文件或配置
- [ ] 如果有副作用，是否标记了 `intrusive`

### 17.5 🧼 语法与格式

- [ ] YAML 是否能通过校验
- [ ] 缩进是否统一
- [ ] 是否存在无关改动
- [ ] 是否保持了仓库整体风格一致

## 📚 18. 推荐参考模板

可以优先参考仓库中以下风格的模板：

- `http/vulnerabilities/nps/nps-auth-bypass.yaml`
- `dast/vulnerabilities/ssti/oob/erb-erubi-erubis-oob.yaml`
- `code/windows/audit/minimum-password-age-zero.yaml`
- `cloud/kubernetes/deployments/k8s-root-container-admission.yaml`

参考时重点观察：

- 字段顺序
- `info` 写法
- `matchers` 设计
- `metadata` 使用
- `flow`、`extractors`、`pre-condition` 的组织方式

## 🧠 19. 最后建议

> ⭐ 如果你只带走一条原则，那就是：每一个请求、每一个 matcher、每一个 tag，都应该能回答“为什么非它不可”。

高质量漏洞模板通常具备以下特征：

- 一眼能看懂检测目标
- 一眼能看懂验证逻辑
- 请求设计克制
- 匹配条件有证据链
- 误报控制有意识
- 元数据完整
- 方便他人复查与复用

如果你在两个实现方案之间犹豫，优先选择：

- 更容易证明漏洞的方案
- 更不容易误报的方案
- 更少副作用的方案
- 更符合仓库已有风格的方案

模板质量的上限，往往不在“语法写对了”，而在于你是否真正理解目标漏洞，以及是否愿意为每一个 matcher 的存在给出理由。
