---
title: Hexo+GitHub+Godaddy+DNSPod绑定到域名
entitle: Hexo-Github-Domain
tags: [博客搭建,域名绑定]
categories:
- 博客搭建
- 域名绑定
date: 2019-05-05 23:36:59

---
Hexo部署到GitPage后绑定博客到自有域名。
<!--more-->

## 挑选域名
首先挑选自己想要的域名，推荐使用[NameBeta](https://namebeta.com/)查询域名是否占用及那些服务商提供折扣。同时NameBeta支持[Chrome插件](https://chrome.google.com/webstore/detail/namebeta-smart-domain-too/opndpgdlkdoeiajepgfdnjedknaohhmg)查询。

![NameBetaWeb](https://ws1.sinaimg.cn/large/0078YTE8gy1g2xqgw92gvj31270sadhy.jpg "NameBeta网页")

![NameBetaPlugin](https://ws1.sinaimg.cn/large/0078YTE8gy1g2xqh3s2cnj30c70hx0t5.jpg  "NameBeta Chrome插件")

## Godaddy域名购买
挑选好之好选择对应的服务商进行购买。我的<www.nopainanymore.me>即在[Godaddy](https://www.godaddy.com/)购买的，支持支付宝支付。

### GitHub配置
在GitHub博客的仓库设置中，设置自定义域名为购买的域名。
![GitHubDomainSetting](https://ws1.sinaimg.cn/large/0078YTE8gy1g2xqhf8a9mj30r00j9ab1.jpg "GitHubDomainSetting")

## DNSPod域名解析配置
使用腾讯云[DNSPod](https://www.dnspod.cn/)进行域名解析使域名可以在国内访问。
在域名记录中添加以下记录：
* CNAME：*****.github.io
* A：185.199.108.153(ping 你的博客地址获取的IP)

![DNSPodRecordSettings](https://ws1.sinaimg.cn/large/0078YTE8gy1g2xqhq5raxj30pl0c0my2.jpg "DNSPodRecordSettings")

![DNSPodSettings](https://ws1.sinaimg.cn/large/0078YTE8gy1g2xqi028glj30nh0d0jrz.jpg "DNSPodSettings")

## GodadyDNS设置
修改Godaddy默认域名DNS解析，将其修改为DNSPod中NS记录对应的记录值，注意去掉最后的.符号。

![GodaddyDNSSetting](https://ws1.sinaimg.cn/large/0078YTE8gy1g2xqi78ykgj31100bsjrj.jpg " GodaddyDNSSetting")


