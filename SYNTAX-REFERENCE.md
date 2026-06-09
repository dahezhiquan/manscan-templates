



## Template 详细语法文档
Template 是一个 YAML 输入文件，用于定义模板中的所有请求及其他元数据。






<hr />

<div class="dd">

<code>id</code>  <i>string</i>

</div>
<div class="dt">

ID 是模板的唯一标识。

#### 合适的 ID

一个合适的 ID 应当能够唯一标识模板中这些请求要完成的事情。比如，如果你的模板用于识别 Web 服务器上的 git-config 文件，一个好的名字可以是 `git-config-exposure`。另一个示例名称是 `azure-apps-nxdomain-takeover`。



示例：


```yaml
# ID 示例
id: CVE-2021-19520
```


</div>

<hr />

<div class="dd">

<code>info</code>  <i><a href="#modelinfo">model.Info</a></i>

</div>
<div class="dt">

Info 包含模板的元数据信息。



示例：


```yaml
info:
    name: Argument Injection in Ruby Dragonfly
    author: 0xspara
    tags: cve,cve2021,rce,ruby
    reference: https://zxsecurity.co.nz/research/argunment-injection-ruby-dragonfly/
    severity: high
```


</div>

<hr />

<div class="dd">

<code>flow</code>  <i>string</i>

</div>
<div class="dt">

说明： |
   Flow 定义模板的执行流程。
 示例：
   - flow: |
 		for region in regions {
		    http(0)
		 }
		 for vpc in vpcs {
		    http(1)
		 }


</div>

<hr />

<div class="dd">

<code>requests</code>  <i>[]<a href="#httprequest">http.Request</a></i>

</div>
<div class="dt">

Requests 包含模板中要执行的 HTTP 请求。
警告：`requests` 将在未来版本中弃用并移除，请改用 `http`。



示例：


```yaml
requests:
    matchers:
        - type: word
          words:
            - '[core]'
        - type: dsl
          condition: and
          dsl:
            - '!contains(tolower(body), ''<html'')'
            - '!contains(tolower(body), ''<body'')'
        - type: status
          status:
            - 200
    matchers-condition: and
    path:
        - '{{BaseURL}}/.git/config'
    method: GET
```


</div>

<hr />

<div class="dd">

<code>http</code>  <i>[]<a href="#httprequest">http.Request</a></i>

</div>
<div class="dt">

说明： |
   HTTP 包含模板中要执行的 HTTP 请求。
 示例：
   - value: exampleNormalHTTPRequest
 RequestsWithHTTP 仅为内部占位占位符，不应直接使用，请改用 RequestsHTTP
 已弃用：请改用 RequestsHTTP。

</div>

<hr />

<div class="dd">

<code>dns</code>  <i>[]<a href="#dnsrequest">dns.Request</a></i>

</div>
<div class="dt">

DNS 包含模板中要执行的 DNS 请求



示例：


```yaml
dns:
    extractors:
        - type: regex
          regex:
            - ec2-[-\d]+\.compute[-\d]*\.amazonaws\.com
            - ec2-[-\d]+\.[\w\d\-]+\.compute[-\d]*\.amazonaws\.com
    name: '{{FQDN}}'
    type: CNAME
    class: inet
    retries: 2
    recursion: false
```


</div>

<hr />

<div class="dd">

<code>file</code>  <i>[]<a href="#filerequest">file.Request</a></i>

</div>
<div class="dt">

File 包含模板中要执行的文件请求



示例：


```yaml
file:
    extractors:
        - type: regex
          regex:
            - amzn\.mws\.[0-9a-f]{8}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{12}
    extensions:
        - all
```


</div>

<hr />

<div class="dd">

<code>network</code>  <i>[]<a href="#networkrequest">network.Request</a></i>

</div>
<div class="dt">

Network 包含模板中要执行的网络请求
警告：`network` 将在未来版本中弃用并移除，请改用 `tcp`。



示例：


```yaml
network:
    host:
        - '{{Hostname}}'
        - '{{Hostname}}:2181'
    inputs:
        - data: "envi\r\nquit\r\n"
    read-size: 2048
    matchers:
        - type: word
          words:
            - zookeeper.version
```


</div>

<hr />

<div class="dd">

<code>tcp</code>  <i>[]<a href="#networkrequest">network.Request</a></i>

</div>
<div class="dt">

说明： |
   TCP 包含模板中要执行的网络请求
 示例：
   - value: exampleNormalNetworkRequest
 RequestsWithTCP 仅为内部占位符，不应直接使用，请改用 RequestsNetwork
 已弃用：请改用 RequestsNetwork。

</div>

<hr />

<div class="dd">

<code>headless</code>  <i>[]<a href="#headlessrequest">headless.Request</a></i>

</div>
<div class="dt">

Headless 包含模板中要执行的无头浏览器请求。

</div>

<hr />

<div class="dd">

<code>ssl</code>  <i>[]<a href="#sslrequest">ssl.Request</a></i>

</div>
<div class="dt">

SSL 包含模板中要执行的 SSL 请求。

</div>

<hr />

<div class="dd">

<code>websocket</code>  <i>[]<a href="#websocketrequest">websocket.Request</a></i>

</div>
<div class="dt">

Websocket 包含模板中要执行的 WebSocket 请求。

</div>

<hr />

<div class="dd">

<code>whois</code>  <i>[]<a href="#whoisrequest">whois.Request</a></i>

</div>
<div class="dt">

WHOIS 包含模板中要执行的 WHOIS 请求。

</div>

<hr />

<div class="dd">

<code>code</code>  <i>[]<a href="#coderequest">code.Request</a></i>

</div>
<div class="dt">

Code 包含代码片段。

</div>

<hr />

<div class="dd">

<code>javascript</code>  <i>[]<a href="#javascriptrequest">javascript.Request</a></i>

</div>
<div class="dt">

Javascript 包含模板中要执行的 JavaScript 请求。

</div>

<hr />

<div class="dd">

<code>self-contained</code>  <i>bool</i>

</div>
<div class="dt">

`self-contained` 将模板中的请求标记为自包含请求

</div>

<hr />

<div class="dd">

<code>stop-at-first-match</code>  <i>bool</i>

</div>
<div class="dt">

一旦找到首个匹配即停止执行

</div>

<hr />

<div class="dd">

<code>signature</code>  <i><a href="#httpsignaturetypeholder">http.SignatureTypeHolder</a></i>

</div>
<div class="dt">

Signature 是请求签名方法
警告：`signature` 将在未来版本中弃用并移除。编写云检查时，建议优先使用 `code` 协议


有效值：


  - <code>AWS</code>
</div>

<hr />

<div class="dd">

<code>variables</code>  <i><a href="#variablesvariable">variables.Variable</a></i>

</div>
<div class="dt">

Variables 包含当前请求可用的变量。

</div>

<hr />

<div class="dd">

<code>constants</code>  <i>map[string]interface{}</i>

</div>
<div class="dt">

Constants 包含当前模板中的标量常量

</div>

<hr />





## model.Info
Info 包含模板的元数据信息

出现于：


- <code><a href="#template">Template</a>.info</code>


```yaml
name: Argument Injection in Ruby Dragonfly
author: 0xspara
tags: cve,cve2021,rce,ruby
reference: https://zxsecurity.co.nz/research/argunment-injection-ruby-dragonfly/
severity: high
```



<hr />

<div class="dd">

<code>name</code>  <i>string</i>

</div>
<div class="dt">

Name 应该是一个简短且准确的概述，用来说明模板的作用。



示例：


```yaml
name: bower.json file disclosure
```

```yaml
name: Nagios Default Credentials Check
```


</div>

<hr />

<div class="dd">

<code>author</code>  <i><a href="#stringslicestringslice">stringslice.StringSlice</a></i>

</div>
<div class="dt">

模板作者。

也可以使用逗号分隔来指定多个值。



示例：


```yaml
author: <username>
```


</div>

<hr />

<div class="dd">

<code>tags</code>  <i><a href="#stringslicestringslice">stringslice.StringSlice</a></i>

</div>
<div class="dt">

模板的标签。

也可以使用逗号分隔来指定多个值。



示例：


```yaml
# 标签示例
tags: cve,cve2019,grafana,auth-bypass,dos
```


</div>

<hr />

<div class="dd">

<code>description</code>  <i>string</i>

</div>
<div class="dt">

模板的描述。

这里可以更详细地说明模板实际完成的工作。



示例：


```yaml
description: Bower is a package manager which stores package information in the bower.json file
```

```yaml
description: Subversion ALM for the enterprise before 8.8.2 allows reflected XSS at multiple locations
```


</div>

<hr />

<div class="dd">

<code>impact</code>  <i>string</i>

</div>
<div class="dt">

模板的影响。

这里可以更详细地说明该模板对应问题的影响。



示例：


```yaml
impact: Successful exploitation of this vulnerability could allow an attacker to execute arbitrary SQL queries, potentially leading to unauthorized access, data leakage, or data manipulation.
```

```yaml
impact: Successful exploitation of this vulnerability could allow an attacker to execute arbitrary script code in the context of the victim's browser, potentially leading to session hijacking, defacement, or theft of sensitive information.
```


</div>

<hr />

<div class="dd">

<code>reference</code>  <i><a href="#stringslicerawstringslice">stringslice.RawStringSlice</a></i>

</div>
<div class="dt">

模板的参考链接。

这里应包含与模板相关的链接。



示例：


```yaml
reference:
    - https://github.com/strapi/strapi
    - https://github.com/getgrav/grav
```


</div>

<hr />

<div class="dd">

<code>severity</code>  <i><a href="#severityholder">severity.Holder</a></i>

</div>
<div class="dt">

模板的严重程度。

</div>

<hr />

<div class="dd">

<code>metadata</code>  <i>map[string]interface{}</i>

</div>
<div class="dt">

模板的元数据。



示例：


```yaml
metadata:
    customField1: customValue1
```


</div>

<hr />

<div class="dd">

<code>classification</code>  <i><a href="#modelclassification">model.Classification</a></i>

</div>
<div class="dt">

Classification 包含模板的分类信息。

</div>

<hr />

<div class="dd">

<code>remediation</code>  <i>string</i>

</div>
<div class="dt">

模板的修复建议。

这里可以更详细地说明如何缓解该模板发现的问题。



示例：


```yaml
remediation: Change the default administrative username and password of Apache ActiveMQ by editing the file jetty-realm.properties
```


</div>

<hr />





## stringslice.StringSlice
StringSlice 表示单个（内联）字符串值或多个字符串值。
 反序列化器不会自动将内联字符串转换为 []string，因此这里需要使用 interface{} 类型。

出现于：


- <code><a href="#modelinfo">model.Info</a>.author</code>

- <code><a href="#modelinfo">model.Info</a>.tags</code>

- <code><a href="#modelclassification">model.Classification</a>.cve-id</code>

- <code><a href="#modelclassification">model.Classification</a>.cwe-id</code>


```yaml
<username>
```
```yaml
# 标签示例
cve,cve2019,grafana,auth-bypass,dos
```
```yaml
CVE-2020-14420
```
```yaml
CWE-22
```





## stringslice.RawStringSlice

出现于：


- <code><a href="#modelinfo">model.Info</a>.reference</code>


```yaml
- https://github.com/strapi/strapi
- https://github.com/getgrav/grav
```





## severity.Holder
Holder 保存一个 Severity 类型，用于序列化和反序列化

出现于：


- <code><a href="#modelinfo">model.Info</a>.severity</code>





<hr />

<div class="dd">

<code></code>  <i>Severity</i>

</div>
<div class="dt">




枚举值：


  - <code>undefined</code>

  - <code>info</code>

  - <code>low</code>

  - <code>medium</code>

  - <code>high</code>

  - <code>critical</code>

  - <code>unknown</code>
</div>

<hr />





## model.Classification

出现于：


- <code><a href="#modelinfo">model.Info</a>.classification</code>





<hr />

<div class="dd">

<code>cve-id</code>  <i><a href="#stringslicestringslice">stringslice.StringSlice</a></i>

</div>
<div class="dt">

模板的 CVE ID



示例：


```yaml
cve-id: CVE-2020-14420
```


</div>

<hr />

<div class="dd">

<code>cwe-id</code>  <i><a href="#stringslicestringslice">stringslice.StringSlice</a></i>

</div>
<div class="dt">

模板的 CWE ID。



示例：


```yaml
cwe-id: CWE-22
```


</div>

<hr />

<div class="dd">

<code>cvss-metrics</code>  <i>string</i>

</div>
<div class="dt">

模板的 CVSS 指标。



示例：


```yaml
cvss-metrics: 3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:H
```


</div>

<hr />

<div class="dd">

<code>cvss-score</code>  <i>float64</i>

</div>
<div class="dt">

模板的 CVSS 分数。



示例：


```yaml
cvss-score: "9.8"
```


</div>

<hr />

<div class="dd">

<code>epss-score</code>  <i>float64</i>

</div>
<div class="dt">

模板的 EPSS 分数。



示例：


```yaml
epss-score: "0.42509"
```


</div>

<hr />

<div class="dd">

<code>epss-percentile</code>  <i>float64</i>

</div>
<div class="dt">

模板的 EPSS 百分位。



示例：


```yaml
epss-percentile: "0.42509"
```


</div>

<hr />

<div class="dd">

<code>cpe</code>  <i>string</i>

</div>
<div class="dt">

模板的 CPE。



示例：


```yaml
cpe: cpe:/a:vendor:product:version
```


</div>

<hr />





## http.Request
HTTP 请求结构，表示模板中要执行的 HTTP 请求

出现于：


- <code><a href="#template">Template</a>.requests</code>

- <code><a href="#template">Template</a>.http</code>


```yaml
matchers:
    - type: word
      words:
        - '[core]'
    - type: dsl
      condition: and
      dsl:
        - '!contains(tolower(body), ''<html'')'
        - '!contains(tolower(body), ''<body'')'
    - type: status
      status:
        - 200
matchers-condition: and
path:
    - '{{BaseURL}}/.git/config'
method: GET
```

组成部分说明： 


- <code>template-id</code> - 已执行模板的 ID
- <code>template-info</code> - 已执行模板的 Info 信息块
- <code>template-path</code> - 已执行模板的路径
- <code>host</code> - Host 是模板的输入目标
- <code>matched</code> - Matched 是触发匹配的输入
- <code>type</code> - Type 是发起请求的类型
- <code>request</code> - 客户端发出的 HTTP 请求
- <code>response</code> - 从服务器收到的 HTTP 响应
- <code>status_code</code> - 从服务器收到的状态码
- <code>body</code> - 从服务器收到的 HTTP 响应体（默认）
- <code>content_length</code> - HTTP 响应内容长度
- <code>header,all_headers</code> - HTTP 响应头
- <code>duration</code> - HTTP 请求耗时
- <code>all</code> - HTTP 响应体 + 响应头
- <code>cookies_from_response</code> - HTTP 响应 Cookie，格式为 name:value
- <code>headers_from_response</code> - HTTP 响应头 in name:value format

<hr />

<div class="dd">

<code>path</code>  <i>[]string</i>

</div>
<div class="dt">

Path 包含 HTTP 请求的路径列表，支持使用变量作为占位符。



示例：


```yaml
# path 示例值
path:
    - '{{BaseURL}}'
    - '{{BaseURL}}/+CSCOU+/../+CSCOE+/files/file_list.json?path=/sessions'
```


</div>

<hr />

<div class="dd">

<code>raw</code>  <i>[]string</i>

</div>
<div class="dt">

Raw 包含原始格式的 HTTP 请求。



示例：


```yaml
# raw 请求示例
raw:
    - |-
      GET /etc/passwd HTTP/1.1
      Host:
      Content-Length: 4
    - |-
      POST /.%0d./.%0d./.%0d./.%0d./bin/sh HTTP/1.1
      Host: {{Hostname}}
      User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:71.0) Gecko/20100101 Firefox/71.0
      Content-Length: 1
      Connection: close

      echo
      echo
      cat /etc/passwd 2>&1
```


</div>

<hr />

<div class="dd">

<code>id</code>  <i>string</i>

</div>
<div class="dt">

ID 是请求的可选标识

</div>

<hr />

<div class="dd">

<code>name</code>  <i>string</i>

</div>
<div class="dt">

Name 是请求的可选名称。

如果指定了名称，模板中所有具名请求都可以被组合匹配，从而支持基于多请求的 matcher。

</div>

<hr />

<div class="dd">

<code>attack</code>  <i><a href="#generatorsattacktypeholder">generators.AttackTypeHolder</a></i>

</div>
<div class="dt">

Attack 表示要执行的 payload 组合方式。

`batteringram` 会将同一个 payload 同时插入所有已定义位置，`pitchfork` 会组合多个 payload 集，而 `clusterbomb` 会生成所有 payload 的排列组合。


有效值：


  - <code>batteringram</code>

  - <code>pitchfork</code>

  - <code>clusterbomb</code>
</div>

<hr />

<div class="dd">

<code>method</code>  <i><a href="#httpmethodtypeholder">HTTPMethodTypeHolder</a></i>

</div>
<div class="dt">

Method 是 HTTP 请求方法。

</div>

<hr />

<div class="dd">

<code>body</code>  <i>string</i>

</div>
<div class="dt">

Body 是一个可选参数，用于包含 HTTP 请求体。



示例：


```yaml
# 登录 POST 请求的示例 Body
body: username=test&password=test
```


</div>

<hr />

<div class="dd">

<code>payloads</code>  <i>map[string]interface{}</i>

</div>
<div class="dt">

Payloads 包含当前请求可用的 payload。

Payloads 同时支持键值组合形式（提供一组 payload 列表），也支持直接提供单个文件作为 payload，该文件会在运行时读取。

</div>

<hr />

<div class="dd">

<code>headers</code>  <i>map[string]string</i>

</div>
<div class="dt">

Headers 包含要随请求发送的 HTTP 头。



示例：


```yaml
headers:
    Any-Header: Any-Value
    Content-Length: "1"
    Content-Type: application/x-www-form-urlencoded
```


</div>

<hr />

<div class="dd">

<code>race_count</code>  <i>int</i>

</div>
<div class="dt">

RaceCount 表示在竞态条件攻击中要发送请求的次数。



示例：


```yaml
# 将请求发送 5 次
race_count: 5
```


</div>

<hr />

<div class="dd">

<code>max-redirects</code>  <i>int</i>

</div>
<div class="dt">

MaxRedirects 表示允许跟随的最大重定向次数。



示例：


```yaml
# 最多跟随 5 次重定向
max-redirects: 5
```


</div>

<hr />

<div class="dd">

<code>pipeline-concurrent-connections</code>  <i>int</i>

</div>
<div class="dt">

PipelineConcurrentConnections 表示在 pipelining 过程中要创建的连接数。



示例：


```yaml
# 创建 40 个并发连接
pipeline-concurrent-connections: 40
```


</div>

<hr />

<div class="dd">

<code>pipeline-requests-per-connection</code>  <i>int</i>

</div>
<div class="dt">

PipelineRequestsPerConnection 表示在 pipelining 时，每个连接要发送的请求数。



示例：


```yaml
# 每个 pipeline 连接发送 100 个请求
pipeline-requests-per-connection: 100
```


</div>

<hr />

<div class="dd">

<code>threads</code>  <i>int</i>

</div>
<div class="dt">

Threads 指定发送请求时使用的线程数，这会启用连接池。

使用 `threads` 时，请求中不能带 `Connection: Close`，否则连接池会失效，引擎仍会在每次请求后关闭连接。



示例：


```yaml
# 使用 10 个并发线程发送请求
threads: 10
```


</div>

<hr />

<div class="dd">

<code>max-size</code>  <i>int</i>

</div>
<div class="dt">

MaxSize 表示要读取的 HTTP 响应体最大字节数。



示例：


```yaml
# 最多读取响应的 2048 字节
max-size: 2048
```


</div>

<hr />

<div class="dd">

<code>fuzzing</code>  <i>[]<a href="#fuzzrule">fuzz.Rule</a></i>

</div>
<div class="dt">

Fuzzing 定义了对 HTTP 请求执行 fuzz 的规则结构。

</div>

<hr />

<div class="dd">

<code>analyzer</code>  <i><a href="#analyzersanalyzertemplate">analyzers.AnalyzerTemplate</a></i>

</div>
<div class="dt">

Analyzer 表示用于匹配响应的分析器。

</div>

<hr />

<div class="dd">

<code>self-contained</code>  <i>bool</i>

</div>
<div class="dt">

SelfContained 指定该请求是否为自包含请求。

</div>

<hr />

<div class="dd">

<code>signature</code>  <i><a href="#signaturetypeholder">SignatureTypeHolder</a></i>

</div>
<div class="dt">

Signature 是请求签名方法


有效值：


  - <code>AWS</code>
</div>

<hr />

<div class="dd">

<code>skip-secret-file</code>  <i>bool</i>

</div>
<div class="dt">

SkipSecretFile 会跳过 secret 文件中配置的认证或授权。

</div>

<hr />

<div class="dd">

<code>cookie-reuse</code>  <i>bool</i>

</div>
<div class="dt">

CookieReuse 是一个可选设置，用于为 `raw` 段中定义的所有请求启用 Cookie 复用。

</div>

<hr />

<div class="dd">

<code>disable-cookie</code>  <i>bool</i>

</div>
<div class="dt">

DisableCookie 是一个可选设置，用于禁用 Cookie 复用。

</div>

<hr />

<div class="dd">

<code>read-all</code>  <i>bool</i>

</div>
<div class="dt">

该选项会强制读取完整的原始不安全请求体，并忽略任何已指定的 `Content-Length` 头。

</div>

<hr />

<div class="dd">

<code>redirects</code>  <i>bool</i>

</div>
<div class="dt">

Redirects 指定 HTTP 客户端是否应跟随重定向。

它可以与 `max-redirects` 配合使用，以控制 HTTP 请求的重定向行为。

</div>

<hr />

<div class="dd">

<code>host-redirects</code>  <i>bool</i>

</div>
<div class="dt">

HostRedirects 指定 HTTP 客户端是否只跟随同一主机上的重定向。

它可以与 `max-redirects` 配合使用，以控制 HTTP 请求的重定向行为。

</div>

<hr />

<div class="dd">

<code>protocol-redirects</code>  <i>bool</i>

</div>
<div class="dt">

ProtocolRedirects 指定是否只跟随相同协议内的重定向。

当该项为 `true` 且启用了 redirects 时，跨协议重定向（例如 HTTP 到 HTTPS）将被阻止。

</div>

<hr />

<div class="dd">

<code>pipeline</code>  <i>bool</i>

</div>
<div class="dt">

Pipeline 指定是否使用 HTTP/1.1 Pipelining 执行该攻击。

所有请求都必须是幂等的（如 GET/POST）。这可用于竞态条件或超高并发请求场景。

</div>

<hr />

<div class="dd">

<code>unsafe</code>  <i>bool</i>

</div>
<div class="dt">

Unsafe 指定是否使用 `rawhttp` 引擎发送不符合 RFC 的请求。

它通过 [rawhttp](https://github.com/projectdiscovery/rawhttp) 引擎实现对请求的完全控制，客户端不会执行任何规范化处理。

</div>

<hr />

<div class="dd">

<code>race</code>  <i>bool</i>

</div>
<div class="dt">

Race 指定是否要同时尝试发送所有请求（竞态条件）。

实际发送的请求数由 `race_count` 字段决定。

</div>

<hr />

<div class="dd">

<code>req-condition</code>  <i>bool</i>

</div>
<div class="dt">

ReqCondition 会自动为请求编号并保留它们的历史记录。

这样后续就可以在多请求条件中对这些结果进行匹配。

</div>

<hr />

<div class="dd">

<code>stop-at-first-match</code>  <i>bool</i>

</div>
<div class="dt">

StopAtFirstMatch 会在找到首个匹配后立即停止请求和模板的执行。

</div>

<hr />

<div class="dd">

<code>skip-variables-check</code>  <i>bool</i>

</div>
<div class="dt">

SkipVariablesCheck 会跳过对请求中未解析变量的检查。

</div>

<hr />

<div class="dd">

<code>iterate-all</code>  <i>bool</i>

</div>
<div class="dt">

IterateAll 会遍历内部提取器提取出的全部值。

</div>

<hr />

<div class="dd">

<code>digest-username</code>  <i>string</i>

</div>
<div class="dt">

DigestAuthUsername 指定 Digest 认证使用的用户名。

</div>

<hr />

<div class="dd">

<code>digest-password</code>  <i>string</i>

</div>
<div class="dt">

DigestAuthPassword 指定 Digest 认证使用的密码。

</div>

<hr />

<div class="dd">

<code>disable-path-automerge</code>  <i>bool</i>

</div>
<div class="dt">

DisablePathAutomerge 会禁用目标 URL 路径与原始请求路径的自动合并。

</div>

<hr />

<div class="dd">

<code>pre-condition</code>  <i>[]<a href="#matchersmatcher">matchers.Matcher</a></i>

</div>
<div class="dt">

Fuzz PreCondition 是一个类似 matcher 的字段，用于检查当前请求是否应执行 fuzz。

</div>

<hr />

<div class="dd">

<code>pre-condition-operator</code>  <i>string</i>

</div>
<div class="dt">

FuzzPreConditionOperator 是多个 fuzz PreCondition 之间的运算符，默认值为 `OR`。

</div>

<hr />

<div class="dd">

<code>global-matchers</code>  <i>bool</i>

</div>
<div class="dt">

GlobalMatchers 将 matchers 标记为静态规则，并全局应用到其他模板的所有结果事件上

</div>

<hr />





## generators.AttackTypeHolder
AttackTypeHolder 用于保存协议内部使用的攻击类型。

出现于：


- <code><a href="#httprequest">http.Request</a>.attack</code>

- <code><a href="#dnsrequest">dns.Request</a>.attack</code>

- <code><a href="#networkrequest">network.Request</a>.attack</code>

- <code><a href="#headlessrequest">headless.Request</a>.attack</code>

- <code><a href="#websocketrequest">websocket.Request</a>.attack</code>

- <code><a href="#javascriptrequest">javascript.Request</a>.attack</code>





<hr />

<div class="dd">

<code></code>  <i>AttackType</i>

</div>
<div class="dt">




枚举值：


  - <code>batteringram</code>

  - <code>pitchfork</code>

  - <code>clusterbomb</code>
</div>

<hr />





## HTTPMethodTypeHolder
HTTPMethodTypeHolder 用于保存 HTTP 方法的内部类型。

出现于：


- <code><a href="#httprequest">http.Request</a>.method</code>





<hr />

<div class="dd">

<code></code>  <i>HTTPMethodType</i>

</div>
<div class="dt">




枚举值：


  - <code>GET</code>

  - <code>HEAD</code>

  - <code>POST</code>

  - <code>PUT</code>

  - <code>DELETE</code>

  - <code>CONNECT</code>

  - <code>OPTIONS</code>

  - <code>TRACE</code>

  - <code>PATCH</code>

  - <code>PURGE</code>

  - <code>Debug</code>
</div>

<hr />





## fuzz.Rule
Rule 是一条用于描述如何对请求执行 fuzz 的规则。

出现于：


- <code><a href="#httprequest">http.Request</a>.fuzzing</code>

- <code><a href="#headlessrequest">headless.Request</a>.fuzzing</code>





<hr />

<div class="dd">

<code>type</code>  <i>string</i>

</div>
<div class="dt">

Type 表示要执行的 fuzz 规则类型。

`replace` 会完全替换值，`prefix` 会在值前添加内容，`postfix` 会在值后追加内容，`infix` 会将内容插入到值之间。


有效值：


  - <code>replace</code>

  - <code>prefix</code>

  - <code>postfix</code>

  - <code>infix</code>
</div>

<hr />

<div class="dd">

<code>part</code>  <i>string</i>

</div>
<div class="dt">

Part 表示要执行 fuzz 的请求部分。


有效值：


  - <code>query</code>

  - <code>header</code>

  - <code>path</code>

  - <code>body</code>

  - <code>cookie</code>

  - <code>request</code>
</div>

<hr />

<div class="dd">

<code>parts</code>  <i>[]string</i>

</div>
<div class="dt">

Parts 表示要执行 fuzz 的多个请求部分列表。如果你需要定义多个部分并排除其中一些，应使用该字段而不是单个 `part`。


有效值：


  - <code>query</code>

  - <code>header</code>

  - <code>path</code>

  - <code>body</code>

  - <code>cookie</code>

  - <code>request</code>
</div>

<hr />

<div class="dd">

<code>mode</code>  <i>string</i>

</div>
<div class="dt">

Mode 表示执行 fuzz 的模式。

`single` 表示一次只 fuzz 一个值，`multiple` 表示同时 fuzz 所有值。


有效值：


  - <code>single</code>

  - <code>multiple</code>
</div>

<hr />

<div class="dd">

<code>keys</code>  <i>[]string</i>

</div>
<div class="dt">

Keys 是一个可选列表，用于指定要 fuzz 的参数键名。



示例：


```yaml
# keys 示例
keys:
    - url
    - file
    - host
```


</div>

<hr />

<div class="dd">

<code>keys-regex</code>  <i>[]string</i>

</div>
<div class="dt">

KeysRegex 是一个可选列表，用于指定要 fuzz 的键名正则。



示例：


```yaml
# 键名正则示例
keys-regex:
    - url.*
```


</div>

<hr />

<div class="dd">

<code>values</code>  <i>[]string</i>

</div>
<div class="dt">

Values 是一个可选列表，用于指定要 fuzz 的值正则。



示例：


```yaml
# 值正则示例
values:
    - https?://.*
```


</div>

<hr />

<div class="dd">

<code>fuzz</code>  <i><a href="#sliceormapslice">SliceOrMapSlice</a></i>

</div>
<div class="dt">

说明： |
   Fuzz 是用于执行替换操作的 payload 列表。
 示例：
   - name: Examples of fuzz
     value: >
       []string{"{{ssrf}}", "{{interactsh-url}}", "example-value"}
      or
       x-header: 1
       x-header: 2

</div>

<hr />

<div class="dd">

<code>replace-regex</code>  <i>string</i>

</div>
<div class="dt">

`replace-regex` 是 `regex-replace` 规则类型使用的正则表达式。
仅在 `replace-regex` 规则类型中需要该字段。

</div>

<hr />





## SliceOrMapSlice

出现于：


- <code><a href="#fuzzrule">fuzz.Rule</a>.fuzz</code>







## analyzers.AnalyzerTemplate
AnalyzerTemplate 是分析器的模板定义。

出现于：


- <code><a href="#httprequest">http.Request</a>.analyzer</code>





<hr />

<div class="dd">

<code>name</code>  <i>string</i>

</div>
<div class="dt">

Name 是要使用的分析器名称。


有效值：


  - <code>time_delay</code>

  - <code>xss_context</code>
</div>

<hr />

<div class="dd">

<code>parameters</code>  <i>map[string]interface{}</i>

</div>
<div class="dt">

Parameters 是分析器的参数。

不同分析器支持的参数各不相同。比如，你可以为 `time_delay` 分析器配置 `sleep_duration`、`time_slope_error_range` 等参数。具体可参考各分析器对应文档。

</div>

<hr />





## SignatureTypeHolder
SignatureTypeHolder 用于保存签名的内部类型。

出现于：


- <code><a href="#httprequest">http.Request</a>.signature</code>







## matchers.Matcher
Matcher 用于匹配协议输出中的某一部分。

出现于：


- <code><a href="#httprequest">http.Request</a>.pre-condition</code>





<hr />

<div class="dd">

<code>type</code>  <i><a href="#matchertypeholder">MatcherTypeHolder</a></i>

</div>
<div class="dt">

Type 表示 matcher 的类型。

</div>

<hr />

<div class="dd">

<code>condition</code>  <i>string</i>

</div>
<div class="dt">

Condition 是多个 matcher 变量之间的可选条件。默认情况下，该条件为 `OR`。


有效值：


  - <code>and</code>

  - <code>or</code>
</div>

<hr />

<div class="dd">

<code>part</code>  <i>string</i>

</div>
<div class="dt">

Part 表示要从响应中取哪一部分进行匹配。

不同协议会暴露出很多可匹配部分，具体请参考各请求类型的文档说明。



示例：


```yaml
part: body
```

```yaml
part: raw
```


</div>

<hr />

<div class="dd">

<code>negative</code>  <i>bool</i>

</div>
<div class="dt">

Negative 指定是否反转匹配结果。
只有当条件不成立时，它才会匹配成功。

</div>

<hr />

<div class="dd">

<code>name</code>  <i>string</i>

</div>
<div class="dt">

Name 是 matcher 的名称。名称应使用小写，并且不能包含空格或下划线（`_`）。



示例：


```yaml
name: cookie-matcher
```


</div>

<hr />

<div class="dd">

<code>status</code>  <i>[]int</i>

</div>
<div class="dt">

Status 是响应允许命中的状态码列表。



示例：


```yaml
status:
    - 200
    - 302
```


</div>

<hr />

<div class="dd">

<code>size</code>  <i>[]int</i>

</div>
<div class="dt">

Size 是响应允许命中的大小列表。



示例：


```yaml
size:
    - 3029
    - 2042
```


</div>

<hr />

<div class="dd">

<code>words</code>  <i>[]string</i>

</div>
<div class="dt">

Words 包含必须出现在响应指定部分中的单词模式。



示例：


```yaml
# 匹配 Outlook 邮件保护域
words:
    - mail.protection.outlook.com
```

```yaml
# 匹配响应头中的 application/json
words:
    - application/json
```


</div>

<hr />

<div class="dd">

<code>regex</code>  <i>[]string</i>

</div>
<div class="dt">

Regex 包含必须出现在响应指定部分中的正则表达式模式。



示例：


```yaml
# 通过正则匹配 Linkerd 服务
regex:
    - (?mi)^Via\\s*?:.*?linkerd.*$
```

```yaml
# 通过 Location 头匹配开放重定向
regex:
    - (?m)^(?:Location\\s*?:\\s*?)(?:https?://|//)?(?:[a-zA-Z0-9\\-_\\.@]*)example\\.com.*$
```


</div>

<hr />

<div class="dd">

<code>binary</code>  <i>[]string</i>

</div>
<div class="dt">

Binary 包含必须出现在响应指定部分中的二进制模式。



示例：


```yaml
# 匹配 Springboot Heapdump Actuator 的 "JAVA PROFILE"、"HPROF"、"Gunzip magic byte"
binary:
    - 4a4156412050524f46494c45
    - 4850524f46
    - 1f8b080000000000
```

```yaml
# 匹配 7zip 文件
binary:
    - 377ABCAF271C
```


</div>

<hr />

<div class="dd">

<code>dsl</code>  <i>[]string</i>

</div>
<div class="dt">

DSL 是会作为 manscan 匹配规则一部分被求值的 DSL 表达式。
这些辅助函数列表可参考[这里](https://nuclei.projectdiscovery.io/templating-guide/helper-functions/)。



示例：


```yaml
# 用于 package.json 文件的 DSL Matcher
dsl:
    - contains(body, 'packages') && contains(tolower(all_headers), 'application/octet-stream') && status_code == 200
```

```yaml
# 用于缺失 strict transport security 头的 DSL Matcher
dsl:
    - '!contains(tolower(all_headers), ''''strict-transport-security'''')'
```


</div>

<hr />

<div class="dd">

<code>xpath</code>  <i>[]string</i>

</div>
<div class="dt">

XPath 是会针对响应指定部分执行求值的 XPath 查询表达式。



示例：


```yaml
# 用于检查标题的 XPath Matcher
xpath:
    - /html/head/title[contains(text(), 'How to Find XPath')]
```

```yaml
# 用于查找带有 target="_blank" 链接的 XPath Matcher
xpath:
    - //a[@target="_blank"]
```


</div>

<hr />

<div class="dd">

<code>encoding</code>  <i>string</i>

</div>
<div class="dt">

Encoding 指定 `words` 字段使用的编码方式（如果有）。


有效值：


  - <code>hex</code>
</div>

<hr />

<div class="dd">

<code>case-insensitive</code>  <i>bool</i>

</div>
<div class="dt">

CaseInsensitive 启用大小写不敏感匹配，默认值为 `false`。


有效值：


  - <code>false</code>

  - <code>true</code>
</div>

<hr />

<div class="dd">

<code>match-all</code>  <i>bool</i>

</div>
<div class="dt">

MatchAll 启用“所有 matcher 值都必须命中”的模式，默认值为 `false`。


有效值：


  - <code>false</code>

  - <code>true</code>
</div>

<hr />

<div class="dd">

<code>internal</code>  <i>bool</i>

</div>
<div class="dt">

说明： |
  当 Internal 为 `true` 时，会将该 matcher 从输出中隐藏，默认值为 `false`。
 它主要用于多协议 / flow 模板，在不输出匹配内容的情况下构造内部 matcher 条件。
 也适用于其他类似场景。
 values:
   - false
   - true

</div>

<hr />





## MatcherTypeHolder
MatcherTypeHolder 用于保存 matcher 的内部类型。

出现于：


- <code><a href="#matchersmatcher">matchers.Matcher</a>.type</code>





<hr />

<div class="dd">

<code></code>  <i>MatcherType</i>

</div>
<div class="dt">




枚举值：


  - <code>word</code>

  - <code>regex</code>

  - <code>binary</code>

  - <code>status</code>

  - <code>size</code>

  - <code>dsl</code>

  - <code>xpath</code>
</div>

<hr />





## dns.Request
DNS 请求结构，表示模板中要执行的 DNS 协议请求

出现于：


- <code><a href="#template">Template</a>.dns</code>


```yaml
extractors:
    - type: regex
      regex:
        - ec2-[-\d]+\.compute[-\d]*\.amazonaws\.com
        - ec2-[-\d]+\.[\w\d\-]+\.compute[-\d]*\.amazonaws\.com
name: '{{FQDN}}'
type: CNAME
class: inet
retries: 2
recursion: false
```

组成部分说明： 


- <code>template-id</code> - 已执行模板的 ID
- <code>template-info</code> - 已执行模板的 Info 信息块
- <code>template-path</code> - 已执行模板的路径
- <code>host</code> - Host 是模板的输入目标
- <code>matched</code> - Matched 是触发匹配的输入
- <code>request</code> - Request 包含文本格式的 DNS 请求
- <code>type</code> - Type 是发起请求的类型
- <code>rcode</code> - Rcode field returned for the DNS request
- <code>question</code> - Question 包含 DNS question 字段
- <code>extra</code> - Extra 包含 DNS 响应的 extra 字段
- <code>answer</code> - Answer 包含 DNS 响应的 answer 字段
- <code>ns</code> - NS 包含 DNS 响应的 NS 字段
- <code>raw,body,all</code> - Raw 包含原始 DNS 响应（默认）
- <code>trace</code> - Trace 包含 DNS 请求的跟踪数据（如果启用）

<hr />

<div class="dd">

<code>id</code>  <i>string</i>

</div>
<div class="dt">

ID 是请求的可选标识

</div>

<hr />

<div class="dd">

<code>name</code>  <i>string</i>

</div>
<div class="dt">

Name 是要发起 DNS 请求的主机名。

通常会设置为 `{{FQDN}}`，也就是从输入中取得的域名。



示例：


```yaml
name: '{{FQDN}}'
```


</div>

<hr />

<div class="dd">

<code>type</code>  <i><a href="#dnsrequesttypeholder">DNSRequestTypeHolder</a></i>

</div>
<div class="dt">

RequestType 表示要发起的 DNS 请求类型。

</div>

<hr />

<div class="dd">

<code>class</code>  <i>string</i>

</div>
<div class="dt">

Class 表示 DNS 请求的类别。

通常保持为 `INET` 即可。


有效值：


  - <code>inet</code>

  - <code>csnet</code>

  - <code>chaos</code>

  - <code>hesiod</code>

  - <code>none</code>

  - <code>any</code>
</div>

<hr />

<div class="dd">

<code>retries</code>  <i>int</i>

</div>
<div class="dt">

Retries 表示 DNS 请求的重试次数。



示例：


```yaml
# 通常可将重试次数设为 3 到 5
retries: 5
```


</div>

<hr />

<div class="dd">

<code>trace</code>  <i>bool</i>

</div>
<div class="dt">

Trace 会对目标执行 trace 操作。

</div>

<hr />

<div class="dd">

<code>trace-max-recursion</code>  <i>int</i>

</div>
<div class="dt">

TraceMaxRecursion 表示 trace 操作允许的最大递归层数。



示例：


```yaml
# 通常可将该值设为 100 到 150
trace-max-recursion: 100
```


</div>

<hr />

<div class="dd">

<code>attack</code>  <i><a href="#generatorsattacktypeholder">generators.AttackTypeHolder</a></i>

</div>
<div class="dt">

Attack 表示要执行的 payload 组合方式。

`batteringram` 会将同一个 payload 同时插入所有已定义位置，`pitchfork` 会组合多个 payload 集，而 `clusterbomb` 会生成所有 payload 的排列组合。

</div>

<hr />

<div class="dd">

<code>payloads</code>  <i>map[string]interface{}</i>

</div>
<div class="dt">

Payloads 包含当前请求可用的 payload。

Payloads 同时支持键值组合形式（提供一组 payload 列表），也支持直接提供单个文件作为 payload，该文件会在运行时读取。

</div>

<hr />

<div class="dd">

<code>threads</code>  <i>int</i>

</div>
<div class="dt">

Threads 表示在遍历 payload 发送请求时使用的线程数。



示例：


```yaml
# 使用 10 个并发线程发送请求
threads: 10
```


</div>

<hr />

<div class="dd">

<code>recursion</code>  <i>dns.bool</i>

</div>
<div class="dt">

Recursion 指定解析器是否应递归查询全部记录以获得最新结果。

</div>

<hr />

<div class="dd">

<code>resolvers</code>  <i>[]string</i>

</div>
<div class="dt">

Resolvers 表示 DNS 请求要使用的解析器列表。

</div>

<hr />





## DNSRequestTypeHolder
DNSRequestTypeHolder 用于保存 DNS 类型的内部表示。

出现于：


- <code><a href="#dnsrequest">dns.Request</a>.type</code>





<hr />

<div class="dd">

<code></code>  <i>DNSRequestType</i>

</div>
<div class="dt">




枚举值：


  - <code>A</code>

  - <code>NS</code>

  - <code>DS</code>

  - <code>CNAME</code>

  - <code>SOA</code>

  - <code>PTR</code>

  - <code>MX</code>

  - <code>TXT</code>

  - <code>AAAA</code>

  - <code>CAA</code>

  - <code>TLSA</code>

  - <code>ANY</code>

  - <code>SRV</code>
</div>

<hr />





## file.Request
File 请求结构，表示用于本地磁盘操作的文件匹配机制。

出现于：


- <code><a href="#template">Template</a>.file</code>


```yaml
extractors:
    - type: regex
      regex:
        - amzn\.mws\.[0-9a-f]{8}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{12}
extensions:
    - all
```

组成部分说明： 


- <code>template-id</code> - 已执行模板的 ID
- <code>template-info</code> - 已执行模板的 Info 信息块
- <code>template-path</code> - 已执行模板的路径
- <code>matched</code> - Matched 是触发匹配的输入
- <code>path</code> - Path is the path of file on local filesystem
- <code>type</code> - Type 是发起请求的类型
- <code>raw,body,all,data</code> - Raw 包含原始文件内容

<hr />

<div class="dd">

<code>extensions</code>  <i>[]string</i>

</div>
<div class="dt">

Extensions 是要执行匹配的扩展名或 MIME 类型列表。



示例：


```yaml
extensions:
    - .txt
    - .go
    - .json
```


</div>

<hr />

<div class="dd">

<code>denylist</code>  <i>[]string</i>

</div>
<div class="dt">

DenyList 是匹配过程中要排除的文件、目录、MIME 类型或扩展名列表。

默认情况下，manscan 会在这里包含一些硬编码的、通常不感兴趣的扩展名。



示例：


```yaml
denylist:
    - .avi
    - .mov
    - .mp3
```


</div>

<hr />

<div class="dd">

<code>id</code>  <i>string</i>

</div>
<div class="dt">

ID 是请求的可选标识

</div>

<hr />

<div class="dd">

<code>max-size</code>  <i>string</i>

</div>
<div class="dt">

MaxSize 表示要执行请求的文件最大大小。

默认情况下，manscan 最多处理 1 GB 内容。你可以根据用途将其设置得更低或更高。
如果设置为 `no`，则会处理全部内容。



示例：


```yaml
max-size: 5Mb
```


</div>

<hr />

<div class="dd">

<code>archive</code>  <i>bool</i>

</div>
<div class="dt">

启用归档文件展开处理。

</div>

<hr />

<div class="dd">

<code>mime-type</code>  <i>bool</i>

</div>
<div class="dt">

启用 MIME 类型检查。

</div>

<hr />

<div class="dd">

<code>no-recursive</code>  <i>bool</i>

</div>
<div class="dt">

NoRecursive 指定在传入文件夹时是否禁用递归检查。

</div>

<hr />





## network.Request
Network 请求结构，表示模板中要执行的 Network 协议请求

出现于：


- <code><a href="#template">Template</a>.network</code>

- <code><a href="#template">Template</a>.tcp</code>


```yaml
host:
    - '{{Hostname}}'
    - '{{Hostname}}:2181'
inputs:
    - data: "envi\r\nquit\r\n"
read-size: 2048
matchers:
    - type: word
      words:
        - zookeeper.version
```

组成部分说明： 


- <code>template-id</code> - 已执行模板的 ID
- <code>template-info</code> - 已执行模板的 Info 信息块
- <code>template-path</code> - 已执行模板的路径
- <code>host</code> - Host 是模板的输入目标
- <code>matched</code> - Matched 是触发匹配的输入
- <code>type</code> - Type 是发起请求的类型
- <code>request</code> - Network request made from the client
- <code>body,all,data</code> - Network response received from server (default)
- <code>raw</code> - Full Network protocol data

<hr />

<div class="dd">

<code>id</code>  <i>string</i>

</div>
<div class="dt">

ID 是请求的可选标识

</div>

<hr />

<div class="dd">

<code>host</code>  <i>[]string</i>

</div>
<div class="dt">

Host 表示要发送网络请求的目标。

通常设置为 `{{Hostname}}`。如果你希望为 TCP 连接启用 TLS，可以使用 `tls://{{Hostname}}`。



示例：


```yaml
host:
    - '{{Hostname}}'
```


</div>

<hr />

<div class="dd">

<code>attack</code>  <i><a href="#generatorsattacktypeholder">generators.AttackTypeHolder</a></i>

</div>
<div class="dt">

Attack 表示要执行的 payload 组合方式。

`batteringram` 会将同一个 payload 同时插入所有已定义位置，`pitchfork` 会组合多个 payload 集，而 `clusterbomb` 会生成所有 payload 的排列组合。

</div>

<hr />

<div class="dd">

<code>payloads</code>  <i>map[string]interface{}</i>

</div>
<div class="dt">

Payloads 包含当前请求可用的 payload。

Payloads support both key-values combinations where a list
of payloads is provided, or optionally a single file can also
be provided as payload which will be read on run-time.

</div>

<hr />

<div class="dd">

<code>threads</code>  <i>int</i>

</div>
<div class="dt">

Threads 指定发送请求时使用的线程数，这会启用连接池。

使用 `threads` 时，请求中不能带 `Connection: Close`，否则连接池会失效，引擎仍会在每次请求后关闭连接。



示例：


```yaml
# 使用 10 个并发线程发送请求
threads: 10
```


</div>

<hr />

<div class="dd">

<code>inputs</code>  <i>[]<a href="#networkinput">network.Input</a></i>

</div>
<div class="dt">

Inputs 包含网络套接字的输入数据

</div>

<hr />

<div class="dd">

<code>port</code>  <i>string</i>

</div>
<div class="dt">

说明： |
   Port 是发送网络请求时使用的端口。它会作为默认端口，但如果目标或输入中包含 80、8080、8081 等非 HTTP(S) 端口，则会被覆盖。支持数字端口和 IANA 服务名（如 ftp、ssh、smtp）。

</div>

<hr />

<div class="dd">

<code>exclude-ports</code>  <i>string</i>

</div>
<div class="dt">

description:	|
	ExcludePorts 是扫描时要排除的端口列表。它通常与 `Port` 字段配合使用，用于声明需要忽略或跳过的端口

</div>

<hr />

<div class="dd">

<code>read-size</code>  <i>int</i>

</div>
<div class="dt">

ReadSize 表示最后要读取的响应大小。

`read-size` 的默认值为 `1024`。



示例：


```yaml
read-size: 2048
```


</div>

<hr />

<div class="dd">

<code>read-all</code>  <i>bool</i>

</div>
<div class="dt">

ReadAll 指定是否无视大小限制，始终将数据流读到结尾。

`read-all` 的默认值为 `false`。



示例：


```yaml
read-all: false
```


</div>

<hr />

<div class="dd">

<code>stop-at-first-match</code>  <i>bool</i>

</div>
<div class="dt">

StopAtFirstMatch 会在找到首个匹配后立即停止请求和模板的执行。

</div>

<hr />





## network.Input

出现于：


- <code><a href="#networkrequest">network.Request</a>.inputs</code>





<hr />

<div class="dd">

<code>data</code>  <i>string</i>

</div>
<div class="dt">

Data 是要作为输入发送的数据。

它支持 DSL 辅助函数以及普通表达式。



示例：


```yaml
data: TEST
```

```yaml
data: hex_decode('50494e47')
```


</div>

<hr />

<div class="dd">

<code>type</code>  <i><a href="#networkinputtypeholder">NetworkInputTypeHolder</a></i>

</div>
<div class="dt">

Type 表示 `data` 字段指定输入的类型。

默认值为 `text`，但也可以使用 `hex` 来表示十六进制格式数据。


有效值：


  - <code>hex</code>

  - <code>text</code>
</div>

<hr />

<div class="dd">

<code>read</code>  <i>int</i>

</div>
<div class="dt">

Read 表示从套接字中读取的字节数。

它适用于那些会立即返回响应的协议。你可以依次读写响应，并最终基于 `name` 属性对捕获到的每段数据执行匹配。

The [network docs](https://nuclei.projectdiscovery.io/templating-guide/protocols/network/) highlight more on how to do this.



示例：


```yaml
read: 1024
```


</div>

<hr />

<div class="dd">

<code>name</code>  <i>string</i>

</div>
<div class="dt">

Name 是已读取数据的可选名称，用于后续匹配。



示例：


```yaml
name: prefix
```


</div>

<hr />





## NetworkInputTypeHolder
NetworkInputTypeHolder 用于保存 Network 输入类型的内部表示。

出现于：


- <code><a href="#networkinput">network.Input</a>.type</code>





<hr />

<div class="dd">

<code></code>  <i>NetworkInputType</i>

</div>
<div class="dt">




枚举值：


  - <code>hex</code>

  - <code>text</code>
</div>

<hr />





## headless.Request
Headless 请求结构，表示模板中要执行的 Headless 协议请求

出现于：


- <code><a href="#template">Template</a>.headless</code>



组成部分说明： 


- <code>template-id</code> - 已执行模板的 ID
- <code>template-info</code> - 已执行模板的 Info 信息块
- <code>template-path</code> - 已执行模板的路径
- <code>host</code> - Host 是模板的输入目标
- <code>matched</code> - Matched 是触发匹配的输入
- <code>type</code> - Type 是发起请求的类型
- <code>req</code> - Headless request made from the client
- <code>resp,body,data</code> - Headless response received from client (default)

<hr />

<div class="dd">

<code>id</code>  <i>string</i>

</div>
<div class="dt">

ID 是请求的可选标识

</div>

<hr />

<div class="dd">

<code>attack</code>  <i><a href="#generatorsattacktypeholder">generators.AttackTypeHolder</a></i>

</div>
<div class="dt">

Attack 表示要执行的 payload 组合方式。

`batteringram` 会将同一个 payload 同时插入所有已定义位置，`pitchfork` 会组合多个 payload 集，而 `clusterbomb` 会生成所有 payload 的排列组合。

</div>

<hr />

<div class="dd">

<code>payloads</code>  <i>map[string]interface{}</i>

</div>
<div class="dt">

Payloads 包含当前请求可用的 payload。

Payloads support both key-values combinations where a list
of payloads is provided, or optionally a single file can also
be provided as payload which will be read on run-time.

</div>

<hr />

<div class="dd">

<code>steps</code>  <i>[]<a href="#engineaction">engine.Action</a></i>

</div>
<div class="dt">

Steps 是 headless 请求要执行的动作列表。

</div>

<hr />

<div class="dd">

<code>user_agent</code>  <i><a href="#useragentuseragentholder">userAgent.UserAgentHolder</a></i>

</div>
<div class="dt">

descriptions: |
 	 User-Agent 是该请求要使用的 user-agent 类型。

</div>

<hr />

<div class="dd">

<code>custom_user_agent</code>  <i>string</i>

</div>
<div class="dt">

说明： |
 	 如果 UserAgent 设置为 `custom`，则 `customUserAgent` 表示该请求要使用的自定义 user-agent。

</div>

<hr />

<div class="dd">

<code>stop-at-first-match</code>  <i>bool</i>

</div>
<div class="dt">

StopAtFirstMatch stops the execution of the requests and template as soon as a match is found.

</div>

<hr />

<div class="dd">

<code>fuzzing</code>  <i>[]<a href="#fuzzrule">fuzz.Rule</a></i>

</div>
<div class="dt">

Fuzzing 定义了对 headless 请求执行 fuzz 的规则结构。

</div>

<hr />

<div class="dd">

<code>cookie-reuse</code>  <i>bool</i>

</div>
<div class="dt">

CookieReuse 是一个可选设置，用于启用 Cookie 复用。

</div>

<hr />

<div class="dd">

<code>disable-cookie</code>  <i>bool</i>

</div>
<div class="dt">

DisableCookie 是一个可选设置，用于禁用 Cookie 复用。

</div>

<hr />





## engine.Action
Action 是浏览器为了完成一次导航而执行的动作。

浏览器执行的每一步都属于一个 action。多数导航通常从 `ActionLoadURL` 事件开始，后续导航则在已发现页面上继续被识别。我们也会跟踪状态，只从尚未抓取过的页面中提取新的导航。

出现于：


- <code><a href="#headlessrequest">headless.Request</a>.steps</code>





<hr />

<div class="dd">

<code>args</code>  <i>map[string]string</i>

</div>
<div class="dt">

Args 包含 headless 动作所需的参数。
各动作对应参数可参考[这里](https://nuclei.projectdiscovery.io/templating-guide/protocols/headless/)的详细说明。

</div>

<hr />

<div class="dd">

<code>name</code>  <i>string</i>

</div>
<div class="dt">

Name 是分配给 headless 动作的名称。

它可用于执行代码，例如在浏览器 DOM 中通过 `script` 动作运行脚本，并将结果保存到一个变量中，供 manscan 后续匹配。示例模板可参考[这里](https://github.com/projectdiscovery/nuclei-templates/blob/main/headless/prototype-pollution-check.yaml)。

</div>

<hr />

<div class="dd">

<code>description</code>  <i>string</i>

</div>
<div class="dt">

Description 是 headless 动作的可选描述。

</div>

<hr />

<div class="dd">

<code>action</code>  <i><a href="#actiontypeholder">ActionTypeHolder</a></i>

</div>
<div class="dt">

Action 表示要执行的动作类型。

</div>

<hr />





## ActionTypeHolder
ActionTypeHolder 用于保存动作的内部类型。

出现于：


- <code><a href="#engineaction">engine.Action</a>.action</code>





<hr />

<div class="dd">

<code></code>  <i>ActionType</i>

</div>
<div class="dt">




枚举值：


  - <code>navigate</code>

  - <code>script</code>

  - <code>click</code>

  - <code>rightclick</code>

  - <code>text</code>

  - <code>screenshot</code>

  - <code>time</code>

  - <code>select</code>

  - <code>files</code>

  - <code>waitdom</code>

  - <code>waitfcp</code>

  - <code>waitfmp</code>

  - <code>waitidle</code>

  - <code>waitload</code>

  - <code>waitstable</code>

  - <code>getresource</code>

  - <code>extract</code>

  - <code>setmethod</code>

  - <code>addheader</code>

  - <code>setheader</code>

  - <code>deleteheader</code>

  - <code>setbody</code>

  - <code>waitevent</code>

  - <code>dialog</code>

  - <code>keyboard</code>

  - <code>debug</code>

  - <code>sleep</code>

  - <code>waitvisible</code>
</div>

<hr />





## userAgent.UserAgentHolder
UserAgentHolder 保存一个 UserAgent 类型，用于序列化和反序列化。

出现于：


- <code><a href="#headlessrequest">headless.Request</a>.user_agent</code>





<hr />

<div class="dd">

<code></code>  <i>UserAgent</i>

</div>
<div class="dt">




枚举值：


  - <code>random</code>

  - <code>off</code>

  - <code>default</code>

  - <code>custom</code>
</div>

<hr />





## ssl.Request
Request 是一个 SSL 协议请求。

出现于：


- <code><a href="#template">Template</a>.ssl</code>



组成部分说明： 


- <code>template-id</code> - 已执行模板的 ID
- <code>template-info</code> - 已执行模板的 Info 信息块
- <code>template-path</code> - 已执行模板的路径
- <code>host</code> - Host 是模板的输入目标
- <code>port</code> - Port is the port of the host
- <code>matched</code> - Matched 是触发匹配的输入
- <code>type</code> - Type 是发起请求的类型
- <code>timestamp</code> - Timestamp is the time when the request was made
- <code>response</code> - JSON SSL protocol handshake details
- <code>cipher</code> - Cipher is the encryption algorithm used
- <code>domains</code> - Domains are the list of domain names in the certificate
- <code>fingerprint_hash</code> - Fingerprint hash is the unique identifier of the certificate
- <code>ip</code> - IP is the IP address of the server
- <code>issuer_cn</code> - Issuer CN is the common name of the certificate issuer
- <code>issuer_dn</code> - Issuer DN is the distinguished name of the certificate issuer
- <code>issuer_org</code> - Issuer organization is the organization of the certificate issuer
- <code>not_after</code> - Timestamp after which the remote cert expires
- <code>not_before</code> - Timestamp before which the certificate is not valid
- <code>probe_status</code> - Probe status indicates if the probe was successful
- <code>serial</code> - Serial is the serial number of the certificate
- <code>sni</code> - SNI is the server name indication used in the handshake
- <code>subject_an</code> - Subject AN is the list of subject alternative names
- <code>subject_cn</code> - Subject CN is the common name of the certificate subject
- <code>subject_dn</code> - Subject DN is the distinguished name of the certificate subject
- <code>subject_org</code> - Subject organization is the organization of the certificate subject
- <code>tls_connection</code> - TLS connection is the type of TLS connection used
- <code>tls_version</code> - TLS version is the version of the TLS protocol used

<hr />

<div class="dd">

<code>id</code>  <i>string</i>

</div>
<div class="dt">

ID 是请求的可选标识

</div>

<hr />

<div class="dd">

<code>address</code>  <i>string</i>

</div>
<div class="dt">

Address 包含请求的地址

</div>

<hr />

<div class="dd">

<code>min_version</code>  <i>string</i>

</div>
<div class="dt">

最小 TLS 版本；如果未指定则自动选择。


有效值：


  - <code>sslv3</code>

  - <code>tls10</code>

  - <code>tls11</code>

  - <code>tls12</code>

  - <code>tls13</code>
</div>

<hr />

<div class="dd">

<code>max_version</code>  <i>string</i>

</div>
<div class="dt">

最大 TLS 版本；如果未指定则自动选择。


有效值：


  - <code>sslv3</code>

  - <code>tls10</code>

  - <code>tls11</code>

  - <code>tls12</code>

  - <code>tls13</code>
</div>

<hr />

<div class="dd">

<code>cipher_suites</code>  <i>[]string</i>

</div>
<div class="dt">

客户端密码套件；如果未指定则自动选择。

</div>

<hr />

<div class="dd">

<code>scan_mode</code>  <i>string</i>

</div>
<div class="dt">

说明： |
   TLS 扫描模式；如果未指定则自动选择。
 values:
   - "ctls"
   - "ztls"
   - "auto"
	 - "openssl" # 如果未安装 openssl，则退回到 "auto"

</div>

<hr />

<div class="dd">

<code>tls_version_enum</code>  <i>bool</i>

</div>
<div class="dt">

TLS 版本枚举；如果未指定则为 `false`。
启用后会枚举受支持的 TLS 版本。

</div>

<hr />

<div class="dd">

<code>tls_cipher_enum</code>  <i>bool</i>

</div>
<div class="dt">

TLS 密码套件枚举；如果未指定则为 `false`。
启用后会枚举受支持的 TLS 密码套件。

</div>

<hr />

<div class="dd">

<code>tls_cipher_types</code>  <i>[]string</i>

</div>
<div class="dt">

说明： |
  要枚举的 TLS 密码套件类型
 values:
   - "insecure"（默认）
   - "weak"
   - "secure"
   - "all"

</div>

<hr />





## websocket.Request
Request 是一个 WebSocket 协议请求。

出现于：


- <code><a href="#template">Template</a>.websocket</code>



组成部分说明： 


- <code>type</code> - Type 是发起请求的类型
- <code>success</code> - Success specifies whether websocket connection was successful
- <code>request</code> - Websocket request made to the server
- <code>response</code> - Websocket response received from the server
- <code>host</code> - Host 是模板的输入目标
- <code>matched</code> - Matched 是触发匹配的输入

<hr />

<div class="dd">

<code>id</code>  <i>string</i>

</div>
<div class="dt">

ID 是请求的可选标识

</div>

<hr />

<div class="dd">

<code>address</code>  <i>string</i>

</div>
<div class="dt">

Address 包含请求的地址

</div>

<hr />

<div class="dd">

<code>inputs</code>  <i>[]<a href="#websocketinput">websocket.Input</a></i>

</div>
<div class="dt">

Inputs 包含 WebSocket 协议的输入数据

</div>

<hr />

<div class="dd">

<code>headers</code>  <i>map[string]string</i>

</div>
<div class="dt">

Headers 包含请求头。

</div>

<hr />

<div class="dd">

<code>attack</code>  <i><a href="#generatorsattacktypeholder">generators.AttackTypeHolder</a></i>

</div>
<div class="dt">

Attack 表示要执行的 payload 组合方式。

`sniper` 会让每个 payload 各执行一次，`pitchfork` 会组合多个 payload 集，而 `clusterbomb` 会生成所有 payload 的排列组合。

</div>

<hr />

<div class="dd">

<code>payloads</code>  <i>map[string]interface{}</i>

</div>
<div class="dt">

Payloads 包含当前请求可用的 payload。

Payloads support both key-values combinations where a list
of payloads is provided, or optionally a single file can also
be provided as payload which will be read on run-time.

</div>

<hr />





## websocket.Input

出现于：


- <code><a href="#websocketrequest">websocket.Request</a>.inputs</code>





<hr />

<div class="dd">

<code>data</code>  <i>string</i>

</div>
<div class="dt">

Data 是要作为输入发送的数据。

它支持 DSL 辅助函数以及普通表达式。



示例：


```yaml
data: TEST
```

```yaml
data: hex_decode('50494e47')
```


</div>

<hr />

<div class="dd">

<code>name</code>  <i>string</i>

</div>
<div class="dt">

Name 是已读取数据的可选名称，用于后续匹配。



示例：


```yaml
name: prefix
```


</div>

<hr />





## whois.Request
Request 是一个 WHOIS 协议请求。

出现于：


- <code><a href="#template">Template</a>.whois</code>





<hr />

<div class="dd">

<code>id</code>  <i>string</i>

</div>
<div class="dt">

ID 是请求的可选标识

</div>

<hr />

<div class="dd">

<code>query</code>  <i>string</i>

</div>
<div class="dt">

Query 包含请求的查询内容

</div>

<hr />

<div class="dd">

<code>server</code>  <i>string</i>

</div>
<div class="dt">

说明： |
 	 可选的 WHOIS 服务器 URL。

 	 如果提供，则指定要在哪个 WHOIS 服务器上执行该请求。
   否则，`nil` 会启用引导发现。

</div>

<hr />





## code.Request
Request 是一个 SSL 协议请求。

出现于：


- <code><a href="#template">Template</a>.code</code>



组成部分说明： 


- <code>type</code> - Type 是发起请求的类型
- <code>host</code> - Host 是模板的输入目标
- <code>matched</code> - Matched 是触发匹配的输入

<hr />

<div class="dd">

<code>id</code>  <i>string</i>

</div>
<div class="dt">

ID 是请求的可选标识

</div>

<hr />

<div class="dd">

<code>engine</code>  <i>[]string</i>

</div>
<div class="dt">

Engine 类型。

</div>

<hr />

<div class="dd">

<code>pre-condition</code>  <i>string</i>

</div>
<div class="dt">

PreCondition 是一个在发送请求前会先被求值的条件。

</div>

<hr />

<div class="dd">

<code>args</code>  <i>[]string</i>

</div>
<div class="dt">

Engine 参数。

</div>

<hr />

<div class="dd">

<code>pattern</code>  <i>string</i>

</div>
<div class="dt">

文件名优先使用的模式。

</div>

<hr />

<div class="dd">

<code>source</code>  <i>string</i>

</div>
<div class="dt">

源文件 / 代码片段。

</div>

<hr />





## javascript.Request
Request 是一个 JavaScript 协议请求。

出现于：


- <code><a href="#template">Template</a>.javascript</code>



组成部分说明： 


- <code>type</code> - Type 是发起请求的类型
- <code>response</code> - Javascript protocol result response
- <code>host</code> - Host 是模板的输入目标
- <code>matched</code> - Matched 是触发匹配的输入

<hr />

<div class="dd">

<code>id</code>  <i>string</i>

</div>
<div class="dt">

说明： |
 ID 是该协议中的请求 ID。

</div>

<hr />

<div class="dd">

<code>init</code>  <i>string</i>

</div>
<div class="dt">

Init 是在模板编译完成后、对任意目标执行之前运行的 JavaScript 代码。
这有助于准备 payload 或其他漏洞利用所需的预置步骤。

</div>

<hr />

<div class="dd">

<code>pre-condition</code>  <i>string</i>

</div>
<div class="dt">

PreCondition 是一个在发送请求前会先被求值的条件。

</div>

<hr />

<div class="dd">

<code>args</code>  <i>map[string]interface{}</i>

</div>
<div class="dt">

Args 包含传递给 JavaScript 代码的参数。

</div>

<hr />

<div class="dd">

<code>code</code>  <i>string</i>

</div>
<div class="dt">

Code 包含要为 JavaScript 请求执行的代码。

</div>

<hr />

<div class="dd">

<code>stop-at-first-match</code>  <i>bool</i>

</div>
<div class="dt">

StopAtFirstMatch 会在第一次命中后停止处理该请求。

</div>

<hr />

<div class="dd">

<code>attack</code>  <i><a href="#generatorsattacktypeholder">generators.AttackTypeHolder</a></i>

</div>
<div class="dt">

Attack 表示要执行的 payload 组合方式。

`sniper` 会让每个 payload 各执行一次，`pitchfork` 会组合多个 payload 集，而 `clusterbomb` 会生成所有 payload 的排列组合。

</div>

<hr />

<div class="dd">

<code>threads</code>  <i>int</i>

</div>
<div class="dt">

Payload 并发数，也就是发送请求时使用的线程数。



示例：


```yaml
# 使用 10 个并发线程发送请求
threads: 10
```


</div>

<hr />

<div class="dd">

<code>payloads</code>  <i>map[string]interface{}</i>

</div>
<div class="dt">

Payloads 包含当前请求可用的 payload。

Payloads support both key-values combinations where a list
of payloads is provided, or optionally a single file can also
be provided as payload which will be read on run-time.

</div>

<hr />





## http.SignatureTypeHolder
SignatureTypeHolder 用于保存签名的内部类型。

出现于：


- <code><a href="#template">Template</a>.signature</code>







## variables.Variable
Variable 是一个可在整个模板中使用的字符串键值对。

出现于：


- <code><a href="#template">Template</a>.variables</code>




