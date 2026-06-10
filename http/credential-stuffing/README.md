## 撞库 Templates


用于检测 某个账号 + 密码，能不能成功登录某个服务


### 模板类型

- **cloud**: 用于对云服务提供商进行撞库测试的模板，比如 Datadog、GitHub、AWS 相关服务
- **Self-Hosted**: 自托管服务模板，比如公司自己搭的 Jira、GitLab、Confluence、Jenkins 等

### Usage

#### 云服务模板用法

以检测 Datadog 登录为例：

- 直接运行，用 -var 传入用户名和密码变量，用 -id 指定模板名称
- 关键点：不需要提供 -u（目标网址），因为目标就是 Datadog 的官方网站，已经写在模板里了

```bash
manscan -var username=testingxxx.io -var password=test123 -id datadog-login-check
```


#### 自托管服务模板用法

以检测 Jira 登录为例：

- 需要通过 -u 选项手动指定你公司 Jira 实例的网址
- 必须提供 -u，因为模板不知道你私有服务器的地址

```bash
manscan -u https://jira.xxx.io/ -id jira-login-check -var username=testing@xxx.io -var password=test123 
```


### 攻击类型（Attack Types）

#### Pitchfork（默认模式）

例如，将 email.txt（账号文件）和 pass.txt（密码文件）按行一对一对应测试

适用场景：当你已知明确的“某邮箱对应某密码”组合，想验证它们是否在其他服务上有效时

#### Cluster Bomb（集束炸弹）

进行笛卡尔积测试。遍历 email.txt 中的每一个账号，分别与 pass.txt 中的每一个密码组合进行测试

适用场景：知道一堆账号和一堆弱密码，想大面积撒网，测试哪些账号使用了这些常见弱密码