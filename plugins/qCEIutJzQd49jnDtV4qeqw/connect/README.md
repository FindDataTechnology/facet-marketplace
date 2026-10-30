# 连接指引（按客户端）

本套件声明了 MCP 引用。**每个客户端走哪条连接道，由机器实证的能力表决定**，不按文档猜：

| 客户端 | 连接道 | 说明 |
| --- | --- | --- |
| workbuddy | L2 | 客户端「workbuddy」不消费静态 client_id、但接受静态 Authorization 头——走代持令牌（L2） |
| CC | L2 | 客户端「CC」未证实用原生 OAuth、但接受静态 Authorization 头——走代持令牌（L2） |
| cursor | L2 | 客户端「cursor」未证实用原生 OAuth、但接受静态 Authorization 头——走代持令牌（L2） |
| zcode | L2 | 客户端「zcode」未证实用原生 OAuth、但接受静态 Authorization 头——走代持令牌（L2） |
| codex | L2 | 客户端「codex」未证实用原生 OAuth、但接受静态 Authorization 头——走代持令牌（L2） |
| gemini-cli | L2 | 客户端「gemini-cli」未证实用原生 OAuth、但接受静态 Authorization 头——走代持令牌（L2） |

## workbuddy（L2）

先跑 `npx @finddatatechnology/facet auth <packRef> --target workbuddy --write-mcp`（它会完成授权并把短命令牌写入客户端配置），再把上面的 Authorization 值替换成命令输出的值；过期后跑 `facet auth --refresh` 静默续期。

### facet-market-mcp

```json
{
  "type": "http",
  "url": "https://mcp.finddatatech.cloud/facet-market-mcp/mcp",
  "headers": {
    "Authorization": "Bearer ${FACET_ACCESS_TOKEN}"
  }
}
```

### facet-ops-mcp

```json
{
  "type": "http",
  "url": "https://mcp.finddatatech.cloud/facet-ops-mcp/mcp",
  "headers": {
    "Authorization": "Bearer ${FACET_ACCESS_TOKEN}"
  }
}
```

## CC（L2）

先跑 `npx @finddatatechnology/facet auth <packRef> --target CC --write-mcp`（它会完成授权并把短命令牌写入客户端配置），再把上面的 Authorization 值替换成命令输出的值；过期后跑 `facet auth --refresh` 静默续期。

### facet-market-mcp

```json
{
  "type": "http",
  "url": "https://mcp.finddatatech.cloud/facet-market-mcp/mcp",
  "headers": {
    "Authorization": "Bearer ${FACET_ACCESS_TOKEN}"
  }
}
```

### facet-ops-mcp

```json
{
  "type": "http",
  "url": "https://mcp.finddatatech.cloud/facet-ops-mcp/mcp",
  "headers": {
    "Authorization": "Bearer ${FACET_ACCESS_TOKEN}"
  }
}
```

## cursor（L2）

先跑 `npx @finddatatechnology/facet auth <packRef> --target cursor --write-mcp`（它会完成授权并把短命令牌写入客户端配置），再把上面的 Authorization 值替换成命令输出的值；过期后跑 `facet auth --refresh` 静默续期。

### facet-market-mcp

```json
{
  "type": "http",
  "url": "https://mcp.finddatatech.cloud/facet-market-mcp/mcp",
  "headers": {
    "Authorization": "Bearer ${FACET_ACCESS_TOKEN}"
  }
}
```

### facet-ops-mcp

```json
{
  "type": "http",
  "url": "https://mcp.finddatatech.cloud/facet-ops-mcp/mcp",
  "headers": {
    "Authorization": "Bearer ${FACET_ACCESS_TOKEN}"
  }
}
```

## zcode（L2）

先跑 `npx @finddatatechnology/facet auth <packRef> --target zcode --write-mcp`（它会完成授权并把短命令牌写入客户端配置），再把上面的 Authorization 值替换成命令输出的值；过期后跑 `facet auth --refresh` 静默续期。

### facet-market-mcp

```json
{
  "type": "http",
  "url": "https://mcp.finddatatech.cloud/facet-market-mcp/mcp",
  "headers": {
    "Authorization": "Bearer ${FACET_ACCESS_TOKEN}"
  }
}
```

### facet-ops-mcp

```json
{
  "type": "http",
  "url": "https://mcp.finddatatech.cloud/facet-ops-mcp/mcp",
  "headers": {
    "Authorization": "Bearer ${FACET_ACCESS_TOKEN}"
  }
}
```

## codex（L2）

先跑 `npx @finddatatechnology/facet auth <packRef> --target codex --write-mcp`（它会完成授权并把短命令牌写入客户端配置），再把上面的 Authorization 值替换成命令输出的值；过期后跑 `facet auth --refresh` 静默续期。

### facet-market-mcp

```json
{
  "type": "http",
  "url": "https://mcp.finddatatech.cloud/facet-market-mcp/mcp",
  "headers": {
    "Authorization": "Bearer ${FACET_ACCESS_TOKEN}"
  }
}
```

### facet-ops-mcp

```json
{
  "type": "http",
  "url": "https://mcp.finddatatech.cloud/facet-ops-mcp/mcp",
  "headers": {
    "Authorization": "Bearer ${FACET_ACCESS_TOKEN}"
  }
}
```

## gemini-cli（L2）

先跑 `npx @finddatatechnology/facet auth <packRef> --target gemini-cli --write-mcp`（它会完成授权并把短命令牌写入客户端配置），再把上面的 Authorization 值替换成命令输出的值；过期后跑 `facet auth --refresh` 静默续期。

### facet-market-mcp

```json
{
  "type": "http",
  "url": "https://mcp.finddatatech.cloud/facet-market-mcp/mcp",
  "headers": {
    "Authorization": "Bearer ${FACET_ACCESS_TOKEN}"
  }
}
```

### facet-ops-mcp

```json
{
  "type": "http",
  "url": "https://mcp.finddatatech.cloud/facet-ops-mcp/mcp",
  "headers": {
    "Authorization": "Bearer ${FACET_ACCESS_TOKEN}"
  }
}
```
