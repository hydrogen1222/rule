# 个人学术网站直连规则（Clash Verge Rev / Mihomo）

这个仓库只维护 **学术网站优先 DIRECT** 的域名列表，不保存机场订阅、代理节点或凭证。当前规则在 [direct.yaml](./direct.yaml) 中，可根据需求继续增加。

## 已包含的站点类别

- **计算化学社区**：思想家公社的门口（`sobereva.com` / `umsyar.com`）、计算化学公社（`keinsci.com`）、`qchem.pw`。
- **文献与引文数据库**：Web of Science、Scopus、Crossref、DOI、ORCID、OpenAlex、Dimensions、PubMed 等。
- **出版商及期刊**：Elsevier / ScienceDirect、Springer Nature、Wiley、ACS、RSC、Science、Cell、PNAS、AIP、APS、IOP、IEEE、ACM、MDPI 等。
- **开放科学**：arXiv、ChemRxiv、bioRxiv、Zenodo、Figshare 等。
- **计算材料**：Materials Project、Materials Cloud、AFLOW、OQMD、NOMAD、CP2K、VASP、QE、LAMMPS 等。
- 国内高校 / 中科院域名：`edu.cn`、`ac.cn`。

**注意：直连不等于可访问或具有数据库权限。** Google Scholar（`scholar.google.com`）和 Google 相关站点默认不强制直连，以免在某些网络环境中打不开。其他国外站点若直连失败，请从规则中移除该域名，或在更靠前的位置设置单独的代理规则。出版商的 PDF 和登录也可能经过第三方域名，需以连接记录排查。

## 配置 Clash Verge Rev（当前版本的图形编辑器）

### 1. 注册规则提供者（一次性）

在当前订阅的 **扩展配置（Extended Configuration）** 中合并以下 YAML（不是直接覆盖机场订阅文件，也不是粘贴进“编辑规则”的输入框）：

```yaml
rule-providers:
  personal-direct:
    type: http
    behavior: classical
    format: yaml
    url: "https://gh-proxy.com/https://raw.githubusercontent.com/hydrogen1222/rule/main/direct.yaml"
    path: ./ruleset/personal-direct.yaml
    interval: 3600
```

如果你已经有 `rule-providers:`，请把 `personal-direct:` 作为其中一个新的提供者添加，不要删除其他提供者。如果使用的是独立的扩展配置文件，只写上方字段即可，其他订阅设置保持原样。

`behavior: classical` 必须和 `direct.yaml` 中的 `DOMAIN-SUFFIX,...` 规则形式相匹配。

若加速域名不可用，可将 URL 改回 `https://raw.githubusercontent.com/hydrogen1222/rule/main/direct.yaml`，或换成可信赖的镜像。第三方 GitHub 加速服务可能缓存规则，且具有内容完整性风险。

### 2. 通过「编辑规则」添加一条前置引用

右键正在使用的订阅 → **编辑规则**，按以下选项填写：

| 项目 | 选择 |
| --- | --- |
| 规则类型 | `RULE-SET` |
| 规则内容 | `personal-direct` |
| 代理策略 | `DIRECT` |
| 跳过 DNS 解析 | 关闭 |
| 操作 | **添加前置规则**，然后保存 |

**不要**把 GitHub URL 填到「规则内容」中，也不要添加后置规则。只有加载了上一步的 `rule-providers.personal-direct`，规则内容下拉列表才会出现 `personal-direct`。如果下拉选项为空，请先保存/启用扩展配置并重新加载当前订阅，再进入规则编辑器。

纯文本等价规则：

```text
RULE-SET,personal-direct,DIRECT
```

### 3. 验证

1. 刷新/重新选择订阅；若提示配置错误，先检查扩展配置和 YAML 的缩进。
2. 查看 Clash Verge Rev 的「规则」页，确认 `RULE-SET,personal-direct,DIRECT` 在原订阅规则之前。
3. 打开 `www.webofscience.com`、`pubs.acs.org` 或 `bbs.keinsci.com`，到「连接」中检查**新建立连接**命中的规则是否是 `personal-direct`，最终出口是否为 `DIRECT`。
4. 如遇到失败页面，先排查 DNS 和校园网/IP 授权，再决定是否将对应域名从强制直连列表中移除。

**更新规则**：只需编辑仓库的 `direct.yaml`，提交后 Mihomo 会依据 `interval: 3600` 定期检查更新。可通过客户端手动刷新规则提供者/订阅或重载配置，但加速站可能存在缓存。

## 维护规则

`direct.yaml` 是 Mihomo 的 **classical YAML rule-provider**，不是完整代理订阅：

```yaml
payload:
  - DOMAIN-SUFFIX,example.org
  - DOMAIN,api.example.net
```

推荐使用 `DOMAIN-SUFFIX` 匹配一个域名和其子域名，不要随意使用 `DOMAIN-KEYWORD,science` 等关键词匹配（容易误伤无关站点）。新的规则要放在 `payload:` 下，保持 2 空格缩进。不要写 `,DIRECT`：`DIRECT` 已由引用规则统一决定。

不建议无差别把 `.com`、`.org`、`.edu` 整个顶级域名直连。对科学出版商和科研平台按域名维护，并结合连接日志迭代。

## 高校授权提示

Web of Science、Scopus 和商业期刊的全文权限常受 IP / SSO / 学校 VPN / 图书馆代理影响；`DIRECT` 只表示不走 Clash 的代理节点，**不能自动获得学校的订阅权限**。校外需要通过学校提供的合法认证通道访问。
