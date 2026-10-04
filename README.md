# Loon-rule

个人 Loon 规则源。`rules/` 下的两份规则来自 8 份本地 Loon 配置的并集；仓库原有的 `Telegram.conf` 和 `Tinder.conf` 保留不变。

## 订阅链接

| 规则源 | 内容 | Loon 中选择的策略 |
| --- | --- | --- |
| [LocalDirect.list](https://raw.githubusercontent.com/robbit69/Loon-rule/master/rules/LocalDirect.list) | 4 条本地网段规则 | `DIRECT` |
| [Anthropic.list](https://raw.githubusercontent.com/robbit69/Loon-rule/master/rules/Anthropic.list) | `claude.ai`、`anthropic.com` 及其子域名 | 现有的 `BWG` 策略组 |

两份规则源按原有策略拆分，文件中只包含匹配条件；策略在 Loon 中指定。后续更新同一路径的文件后，刷新 Loon 订阅即可获取新内容，无需更换链接。

## 在 Loon 中使用

在订阅规则列表中分别添加上面的两个链接，启用规则，选择对应策略。使用其他代理策略组名称时，将 `BWG` 改为该组的名称。

也可以将下面的配置片段合并到当前配置对应的段落中：

```ini
[Rule]
FINAL,BWG

[Remote Rule]
https://raw.githubusercontent.com/robbit69/Loon-rule/master/rules/LocalDirect.list,policy=DIRECT,tag=LocalDirect,enabled=true
https://raw.githubusercontent.com/robbit69/Loon-rule/master/rules/Anthropic.list,policy=BWG,tag=Anthropic,enabled=true
```

`FINAL,BWG` 保留在本地 `[Rule]` 段落的最后，表示未命中其他规则的请求走 `BWG`。它没有计入两个订阅文件的规则条数。本地规则去重后的并集共 7 条，其中 6 条转换为订阅规则，1 条作为本地兜底保留。

替换原有规则时，删除原来的 4 条本地网段规则和 2 条 Anthropic 域名规则，保留其余本地规则、节点和策略组。若已有 `[Remote Rule]` 段落，只需追加两行订阅，不要重复创建段落。其他本地规则、插件和订阅仍可能影响最终分流；该片段只复现此次提取的 7 条规则。

[examples/local-union.conf](examples/local-union.conf) 是上述用法的配置片段，没有节点信息，请合并使用。

## 并集来源

提取日期：2026-10-04（北京时间）。按下面的文件顺序读取各文件的 `[Rule]` 段落，忽略空行和注释，按规则字段去除两侧空格后求并集：完整规则相同时只保留首次出现的一条，不要求每个文件都含有该规则。保留首次出现的顺序，兜底规则放在本地规则最后。只提取规则；源配置本身没有上传。

- `clients/loon-ios-loon.conf`
- `migration-104/clients/loon-ios-loon.conf`
- `clients/loon-android.conf`
- `clients/loon-macos.conf`
- `clients/loon-windows.conf`
- `migration-104/clients/loon-android.conf`
- `migration-104/clients/loon-macos.conf`
- `migration-104/clients/loon-windows.conf`

8 份配置的规则完全一致，共读取 56 条规则，去重后的并集为 7 条。此次改为并集后，两个订阅文件的匹配条件与原发布内容一致，订阅链接也保持不变：

```ini
IP-CIDR,127.0.0.0/8,DIRECT,no-resolve
IP-CIDR,10.0.0.0/8,DIRECT,no-resolve
IP-CIDR,172.16.0.0/12,DIRECT,no-resolve
IP-CIDR,192.168.0.0/16,DIRECT,no-resolve
DOMAIN-SUFFIX,claude.ai,BWG
DOMAIN-SUFFIX,anthropic.com,BWG
FINAL,BWG
```

## 格式参考

- [Loon 官方订阅规则示例](https://github.com/Loon0x00/LoonExampleConfig/blob/master/Rule/ExampleRule.list)
- [Loon 官方远程规则配置示例](https://github.com/Loon0x00/LoonExampleConfig/blob/master/example.conf)
- [Loon 官方 FINAL 规则说明](https://github.com/Loon0x00/LoonManual/blob/master/docs/cn/final_rule.md)

订阅文件保留 `no-resolve`，不写入本地策略名；使用 `[Remote Rule]` 中的 `policy` 指定策略。只发布此次提取的规则和使用说明，不包含源配置的代理服务器、密码、UUID、私人订阅地址或证书。
