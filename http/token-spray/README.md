## Token 喷洒模版 ☠️

当你手里拿到一个**来源不明的 token / API key** 时，可以调用此目录下的模版，ManScan 会把这个 token 去请求一批**预置好的静态 API 端点**，看看它对哪些服务有效，从而判断：**这个 token 属于哪个平台、是否可用、权限是否仍然有效**

## Usage

通过命令行参数传入 `Token`，然后批量跑这个目录的模版

```console
# 单个 Token 测试
manscan -t token-spray/ -var token=random-token-to-test -esc

# 批量测试
manscan -t token-spray/ -var token=file_with_tokens.txt -esc
```

