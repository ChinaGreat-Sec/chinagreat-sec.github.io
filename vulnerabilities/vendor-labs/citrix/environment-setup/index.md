---
layout: default
title: "Citrix NetScaler 环境搭建"
permalink: /vulnerabilities/vendor-labs/citrix/environment-setup/
category: vulnerability
tags:
  - Citrix
  - NetScaler
  - Citrix ADC
  - environment-setup
vendor: Citrix
product: NetScaler ADC
author: ChinaGreat-IoTSec
date: 2026-08-21
---

# Citrix NetScaler 环境搭建

<p>
<img src="https://img.shields.io/badge/类型-环境搭建-orange" alt="Type">
<img src="https://img.shields.io/badge/平台-EVE--NG-blue" alt="Platform">
<img src="https://img.shields.io/badge/厂商-Citrix%20NetScaler-lightgrey" alt="Vendor">
</p>

---

## 摘要

本文记录在 EVE-NG 中搭建 Citrix NetScaler / Citrix ADC 测试环境的过程，覆盖镜像准备、节点启动、管理 IP 初始化、Web 管理页面访问、Gateway 功能配置和常见证书问题处理。

本文仅用于合法授权的安全研究与教学环境搭建，不提供商业镜像、授权绕过或非法使用方法。

## 基本信息

| 项目 | 内容 |
| --- | --- |
| 厂商 | Citrix |
| 产品 | NetScaler ADC / Citrix ADC |
| 环境平台 | EVE-NG |
| 文章类型 | 环境搭建 |
| 适用场景 | Citrix Gateway / ADC 漏洞复现与安全研究准备 |
| 默认账号 | `nsroot` |
| 默认密码 | `nsroot` |

## 镜像准备

现有途径可通过闲鱼、论坛、EVE 资源网盘等渠道获取设备镜像。拥有 Citrix 账户的用户可以通过官方渠道下载，目前个人用户注册 Citrix 官方账号存在限制。

Catalpa 的文章中提供了相关镜像获取线索，可参考：

- [Catalpa - CVE-2023-4966](https://wzt.ac.cn/2023/10/24/CVE-2023-4966/)
- [EVE 资源网盘帖子](https://www.emulatedlab.com/thread-939-1-1.html)

关于镜像获取途径和文件形式，不再逐一展开。请确保镜像来源和使用方式符合授权要求。

## 环境配置

实验使用 EVE-NG 进行演示。通过 EVE-NG 启动 Citrix NetScaler 的过程可参考官方文档：

- [EVE-NG - HowTo add Citrix NetScaler](https://www.eve-ng.net/index.php/documentation/howtos/howto-add-citrix-netscaler/)

![EVE-NG Citrix NetScaler 官方配置参考]({{ '/assets/images/vulnerabilities/vendor-labs/citrix/environment-setup/IMG-20260821015315667.png' | relative_url }})

### 1. 启动镜像

创建网络时选择 `management`，即第一个网卡。随后创建 Citrix NetScaler 节点。

Citrix NetScaler 的管理口是 `0/1`，因此创建完节点后，需要将 `0/1` 网卡连接到 `management` 网络。

![创建 Citrix NetScaler 节点]({{ '/assets/images/vulnerabilities/vendor-labs/citrix/environment-setup/IMG-20260821015721294.png' | relative_url }})

![连接管理网络]({{ '/assets/images/vulnerabilities/vendor-labs/citrix/environment-setup/IMG-20260821015609582.png' | relative_url }})

启动设备后，Citrix NetScaler 默认无法通过 DHCP 获取 IP，需要先通过 EVE-NG 管理口连接。

打开浏览器开发者选项，双击新建的 Citrix NetScaler 设备，会弹出连接请求。通过浏览器开发者窗口可以看到连接协议、IP 和端口，再使用 PuTTY 等连接工具进行连接。

![查看 EVE-NG 连接请求]({{ '/assets/images/vulnerabilities/vendor-labs/citrix/environment-setup/IMG-20260821020018147.png' | relative_url }})

![使用连接工具接入控制台]({{ '/assets/images/vulnerabilities/vendor-labs/citrix/environment-setup/IMG-20260821020321516.png' | relative_url }})

连接会话后按下回车，出现登录提示即表示设备启动成功。

### 2. 初始化配置

默认账号：

```text
nsroot
```

默认密码：

```text
nsroot
```

首次登录需要重置密码。登录后进入 `nscli`，输入 `shell` 即可进入底层 `sh`。

![首次登录并进入系统]({{ '/assets/images/vulnerabilities/vendor-labs/citrix/environment-setup/IMG-20260821020723632.png' | relative_url }})

随后配置管理 IP：

- 在 `sh` 中输入 `nsconfig`
- 在 `nscli` 中输入 `config ns`

只需要配置：

1. IP 地址
2. 掩码

配置完成后选择 `7` 保存。选择 `7` 时会提示重启，配置需要重启后才会生效。

![配置管理 IP]({{ '/assets/images/vulnerabilities/vendor-labs/citrix/environment-setup/IMG-20260821021258642.png' | relative_url }})

重启后，通过浏览器访问配置的 IP 地址。如果出现 Web 登录页面，则说明管理地址配置成功。

登录账号为 `nsroot`，密码为首次登录时重置后的密码。

![访问 Web 管理页面]({{ '/assets/images/vulnerabilities/vendor-labs/citrix/environment-setup/IMG-20260821021836483.png' | relative_url }})

### 3. 授权说明

登录管理页面后，可以看到当前授权状态。如果是免费授权，通常不包含 Gateway、VPN 等功能。

![查看授权状态]({{ '/assets/images/vulnerabilities/vendor-labs/citrix/environment-setup/IMG-20260821022044453.png' | relative_url }})

授权应优先通过官方或合法渠道申请。部分公开研究文章会提到本地测试授权方式，本文仅引用相关研究背景，不展开具体程序、绕过流程或操作细节。

完成合法授权后，管理界面可使用 Gateway 等功能。多数 Citrix Gateway 相关 CVE 复现需要 Gateway 功能支持。

![授权后功能页面]({{ '/assets/images/vulnerabilities/vendor-labs/citrix/environment-setup/IMG-20260821022452376.png' | relative_url }})

### 4. Gateway 配置

授权开启 Gateway 功能后，点击模块中的 `NetScaler Gateway Wizard` 选项进行配置。

![打开 NetScaler Gateway Wizard]({{ '/assets/images/vulnerabilities/vendor-labs/citrix/environment-setup/IMG-20260821023525402.png' | relative_url }})

`Gateway IP Address` 需要和 Web 管理 IP 位于同一个网段，但不能使用同一个 IP。

![配置 Gateway IP Address]({{ '/assets/images/vulnerabilities/vendor-labs/citrix/environment-setup/IMG-20260821023805939.png' | relative_url }})

随后为 Gateway 配置证书。可以本地生成 RSA 证书后通过 `Install Certificate` 上传配置，也可以使用 `Create Test Certificate` 创建测试证书。

![配置 Gateway 证书]({{ '/assets/images/vulnerabilities/vendor-labs/citrix/environment-setup/IMG-20260821024020746.png' | relative_url }})

之后根据漏洞复现需求配置本地用户。

![配置本地用户]({{ '/assets/images/vulnerabilities/vendor-labs/citrix/environment-setup/IMG-20260821024350113.png' | relative_url }})

配置完成后，访问配置的 Gateway IP 地址。如果出现登录页面，则说明 Gateway 访问成功。

如果浏览器出现证书错误 `ERR_SSL_KEY_USAGE_INCOMPATIBLE`，需要本地生成证书并上传绑定。该证书错误属于浏览器安全策略问题，不影响漏洞复现；如果不需要登录 Gateway 功能，可以暂时忽略。

## 常见问题

### 设备无法通过 DHCP 获取 IP

Citrix NetScaler 默认无法通过 DHCP 自动获取管理地址，需要通过控制台进入系统后手动配置管理 IP。

### Gateway IP 是否可以和管理 IP 相同

不建议相同。`Gateway IP Address` 应与管理 IP 位于同一网段，但不能使用同一个 IP。

### 证书错误是否影响漏洞复现

一般不影响基础漏洞复现。如果复现链路需要完整登录 Gateway，建议重新生成符合浏览器要求的证书并绑定。

## 参考资料

- [EVE-NG - HowTo add Citrix NetScaler](https://www.eve-ng.net/index.php/documentation/howtos/howto-add-citrix-netscaler/)
- [Catalpa - CVE-2023-4966](https://wzt.ac.cn/2023/10/24/CVE-2023-4966/)
- [EVE 资源网盘帖子](https://www.emulatedlab.com/thread-939-1-1.html)

## 免责声明

> 本文仅用于安全研究与教育目的。请勿将上述内容用于未经授权的系统、网络或商业环境。
