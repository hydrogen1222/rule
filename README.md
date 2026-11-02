# Personal Mihomo routing rules

This repository stores custom routing rules for Clash Verge Rev / Mihomo, separately from your proxy subscription.

## Direct rules

Edit [`direct.yaml`](./direct.yaml) to add websites that must bypass proxies. Example:

```yaml
payload:
  - DOMAIN-SUFFIX,example.com
  - DOMAIN,api.example.net
```

`DOMAIN-SUFFIX` matches the domain and its subdomains. `DOMAIN` matches one exact host. Replace the example domains with your desired sites.

## Clash Verge Rev setup

In the subscription's **Extension Configuration** add the following provider definition (do **not** replace the full subscription):

```yaml
rule-providers:
  personal-direct:
    type: http
    behavior: classical
    format: yaml
    url: https://raw.githubusercontent.com/hydrogen1222/rule/main/direct.yaml
    path: ./ruleset/personal-direct.yaml
    interval: 3600
```

Then right-click the subscription profile and open **Edit Rules**. Add this to **Prepend**, above the provider's existing rules:

```text
RULE-SET,personal-direct,DIRECT
```

Save and refresh/reselect the subscription. Check the resulting rules and connection logs to verify that the domain matches `personal-direct` and uses `DIRECT`.

**Note:** The direct.yaml file is only a rule-provider payload, not a standalone Clash subscription. Do not enter its URL as your main subscription. The provider URL is public, so do not put personal secrets, subscriptions, or access tokens in this repository. The example entry should be replaced before actual use.
