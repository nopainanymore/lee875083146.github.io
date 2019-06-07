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
首先挑选自己想要的域名，推荐使用[NameBeta](https://namebeta.com/)查询域名是否占用及哪些服务商提供折扣。同时NameBeta支持[Chrome插件](https://chrome.google.com/webstore/detail/namebeta-smart-domain-too/opndpgdlkdoeiajepgfdnjedknaohhmg)查询。

![NameBetaWeb](https://nopainanymore.oss-cn-hangzhou.aliyuncs.com/GitPages/NameBetaWeb.PNG?x-oss-process=style/sw-white "NameBeta网页")

![NameBetaPlugin](https://nopainanymore.oss-cn-hangzhou.aliyuncs.com/GitPages/NameBetaPlugin.PNG?x-oss-process=style/sw-white  "NameBeta Chrome插件")

## Godaddy域名购买
挑选好之好选择对应的服务商进行购买。我的<www.nopainanymore.me>即在[Godaddy](https://www.godaddy.com/)购买的，支持支付宝支付。

### GitHub配置
在GitHub博客的仓库设置中，设置自定义域名为购买的域名。
![GitHubDomainSetting](https://nopainanymore.oss-cn-hangzhou.aliyuncs.com/GitPages/GitHubDomainSetting.png?x-oss-process=style/sw-white "GitHubDomainSetting")

## DNSPod域名解析配置
使用腾讯云[DNSPod](https://www.dnspod.cn/)进行域名解析使域名可以在国内访问。
在域名记录中添加以下记录：
* CNAME：*****.github.io
* A：185.199.108.153(ping 你的博客地址获取的IP)

![DNSPodRecordSettings](https://nopainanymore.oss-cn-hangzhou.aliyuncs.com/GitPages/DNSPodRecordSettings.png?x-oss-process=style/sw-white "DNSPodRecordSettings")

![DNSPodSettings](https://nopainanymore.oss-cn-hangzhou.aliyuncs.com/GitPages/DNSPodSettings.PNG?x-oss-process=style/sw-white "DNSPodSettings")

## GodadyDNS设置
修改Godaddy默认域名DNS解析，将其修改为DNSPod中NS记录对应的记录值，注意去掉最后的.符号。

![GodaddyDNSManagement](https://nopainanymore.oss-cn-hangzhou.aliyuncs.com/GitPages/GodaddyDNSManagement.PNG?x-oss-process=style/sw-white " GodaddyDNSManagement")


