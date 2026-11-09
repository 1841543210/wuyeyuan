# wuyeyuan 影视源 · KVideo 订阅说明

本仓库的 `sources.json`（TVBox 风格，共 120 个源）已转换为 **KVideo 官方兼容的订阅格式**，
生成的订阅文件为 [`kvideo-sources.json`](kvideo-sources.json)。原始文件
`sources.json`、`sources_list.txt` **未做任何改动**。

转换依据为 KVideo 官方仓库（KuekHaoYang/KVideo）当前代码中的订阅解析逻辑
`lib/utils/source-import-utils.ts`，并已用该官方解析函数实测验证通过（120/120 条可解析）。

## 一、订阅地址（直接填入 KVideo）

```
https://raw.githubusercontent.com/1841543210/wuyeyuan/main/kvideo-sources.json
```

## 二、如何导入 KVideo

1. 打开 KVideo（Docker 版或自托管版均可），进入 **设置 → 订阅管理**。
2. 点击添加订阅：
   - **名称**：任意，例如 `wuyeyuan`
   - **链接**：填入上面的订阅地址
3. 保存后等待拉取完成（订阅支持自动刷新，也可点刷新按钮手动更新）。
4. 本订阅中的源属于 KVideo 的 **premium（成人源）分组**——KVideo 导入界面即把该分组标注为「成人源」。
   导入后请在 **高级源管理** 中查看这些源，在 **高级模式（成人模式）** 中浏览和搜索使用；
   首次进入高级内容时，按提示设置/输入高级内容密码。

> 注意：订阅地址指向的是一个 JSON **配置文件**，不是单个视频 API。
> 不要把某个源的 API 地址直接当订阅添加。

## 三、如何更新订阅

- KVideo 端：订阅默认自动刷新；在 **设置 → 订阅管理** 中点对应订阅的刷新按钮可立即同步。
- 仓库端：本仓库的 `kvideo-sources.json` 有更新时，KVideo 刷新后即生效，无需重新添加订阅。

## 四、如何查看影视源状态

每个源都做过真实接口检测（不是只看 HTTP 200），完整结果在
[`source-report.json`](source-report.json)（Raw 地址：
`https://raw.githubusercontent.com/1841543210/wuyeyuan/main/source-report.json`），
包含每个源的列表、搜索、详情、播放四步检测结果与状态分类。

本次检测汇总（2026-11-09，检测结果会随各源站状态变化）：

| 状态 | 数量 | 含义 |
|---|---|---|
| 正常 | 52 | 搜索、详情、播放地址均实测通过 |
| 连接失败 | 27 | 无法连接或 HTTP 错误 |
| 格式不兼容 | 20 | 接口返回 XML，KVideo 只解析 JSON，在 KVideo 内不可用 |
| 播放地址异常 | 15 | 详情有返回，但播放地址无法连接或无效 |
| 搜索失败 | 5 | 列表可用但搜索无有效返回 |
| 详情失败 | 1 | 详情接口无有效返回 |

订阅文件中保留了全部 120 个源（包括暂时不可用的），状态以 `source-report.json` 为准，
你可以在 KVideo 的高级源管理中自行停用失效的源。

## 五、字段转换说明

| 原始字段（sources.json） | KVideo 字段 | 说明 |
|---|---|---|
| `key` | `id` | 沿用原始唯一编号，无重复 |
| `name` | `name` | 原样保留 |
| `api` | `baseUrl` | 原样保留，未做任何修改 |
| `type`（全部为 1） | — | KVideo 格式无 type 字段，其接口原生即 MacCMS JSON API，与 type=1 对应 |
| — | `group` | 统一为 `premium`（KVideo 的成人源分组） |
| — | `priority` | 按原始顺序编号 |
| `searchable` / `quickSearch` / `filterable` | — | KVideo 官方格式无此三字段（其搜索统一走 `ac=detail&wd=`），原值已记录在 `source-report.json` 每条记录中 |

KVideo 订阅导入时 `searchPath`、`detailPath` 为空，实际请求即
`{baseUrl}?ac=detail&wd=关键词`（搜索）与 `{baseUrl}?ac=detail&ids=ID`（详情），
因此接口返回 XML 的源无法被 KVideo 解析，已在报告中单独标为「格式不兼容」。
