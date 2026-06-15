## 开源情报（OSINT）探测

可在海量网站上开展用户名枚举侦察。只需通过参数传入目标用户名，ManScan 就能调用内置模板批量检测该账号是否在各大平台注册存在，并输出所有探测成功的结果

## Usage

存放于该目录下的 OSINT 模板均为独立完备模板，无需手动输入网址，模板内部已预配置好各平台固定静态链接

该目录下所有模板，只需通过引擎的 -V/var 参数，传入用户名、邮箱、手机号，或是包含这些信息的列表文件即可运行

### 探测单个用户名

```bash
manscan -tags osint -var user=dahe
```
### 批量探测用户名列表

先准备用户名列表文件 user_names.txt

```bash
manscan -tags osint -var user=user_names.txt
```

## 模板分类

OSINT 模板按应用场景划分为多类：归档、艺术、博客、商业、编程、交友、金融、游戏、健康、兴趣爱好、图片、杂项、音乐、新闻、政务、搜索、购物、社交、科技、视频、成人等类别

对应的 `tag` 标签后缀如下：

`archived`, `art`, `blog`, `business`, `coding`, `dating`, `finance`, `gaming`, `health`, `hobby`, `images`, `misc`, `music`, `news`, `political`, `search`, `shopping`, `social`, `tech`, `video`, `porn`

可通过 `tags` 参数以 `osint-` 为前缀筛选指定类别模板运行：

```bash
# 仅扫描社交类平台
manscan -t osint/ -tags osint-social -var user=some-user

# 同时扫描社交、金融多类别
manscan -t osint/ -tags osint-social,osint-finance -var user=some-user
```
