---
title: "VPN"
date: 2022-11-18T10:24:01+09:00
draft: true
hero: network.png
menu:
  sidebar:
    name: VPN
    identifier: VPN
    parent: 网络
    weight: 130
---

> “KDSP” 在开始之前，您需要从 StrongVPN 的客户区域获取您的 VPN 帐户凭据。

“KDSP” 在开始之前，您需要从 StrongVPN 的客户区域获取您的 VPN 帐户凭据。要登录客户区，您需要使用您与我们的电子邮件作为登录。密码是您第一次下订单时创建的密码（当然，如果您从那时起没有更改）。您可以从 StrongVPN 网站登录，顶部有一个链接：

![](https://strongtech.org/assets/img/tutorials/find_setup_credentials_general_step_01-7bb888dc06.png)

如果不起作用，则直接链接到客户区域登录页面：https://intranet.strongtech.org/services/intranet/

如果无法记住密码，请使用以下链接重置密码：https://intranet.strongtech.org/services/intranet/password_reset/

“KDSP”登录后，将鼠标悬停在顶部的 “VPN 帐户” 上，然后单击菜单项“VPN 帐户摘要”。

![](https://strongtech.org/assets/img/tutorials/find_setup_credentials_general_step_02-b93debb999.png)

单击 OpenVPN/IPSec 帐户附近的 “获取 OpenVPN 配置文件”。

![](https://strongtech.org/assets/img/tutorials/find_setup_credentials_openvpn_step_03-8335a04ced.png)

向下滚动页面，您将找到配置文件，它是安装所必需的，请下载它。

根据 OpenVPN 服务器设置，您将看到一个或两个下载按钮，如果您看到两个，请下载 “Linux/Mac 配置文件”，无论您正在设置哪个设备。Windows 配置文件仅用于 Windows 系统上的 OpenVPN GUI 应用程序设置。

![](https://strongtech.org/assets/img/tutorials/find_setup_credentials_openvpn_step_04-f56ddde65d.png)![](https://strongtech.org/assets/img/tutorials/find_setup_credentials_openvpn_step_05-24096b84cc.png)

如果您在此页面上使用我们的新型 OpenVPN 服务器，您还将看到您的帐户设置凭据：用户名和密码。如果您没有看到它们，请忽略提到用户名和密码的步骤。

![](https://strongtech.org/assets/img/tutorials/find_setup_credentials_openvpn_step_06-f0e34fb803.png)

请注意：上面屏幕上的凭据将不起作用 - 这只是一个示例。

我们建议将 “帐户设置说明” 窗口保持打开状态，因为您需要此信息进行设置。在完成之前，确保您手头有凭证。

本教程旨在用于 AsusWRT Merlin 固件。在路由器界面网页的顶部，您将看到向导帽的图像和标题 “由 Asuswrt Merlin 供电”，如下图所示。

![](https://strongtech.org/assets/img/tutorials/setup_asuswrt_merlin_openvpn_step_00-fc286a29da.png)

如果您没有这样的映像，这意味着您的路由器有库存固件（两个固件接口非常相似），您应该使用华硕路由器库存固件 OpenVPN 设置教程。如果按照路由器库存固件上 AsusWRT Merlin 固件的说明操作，您将错过防止 DNS 泄漏的必要步骤，并且某些服务将无法工作。

默认信息：

默认路由器 IP 地址：192.168。1.1 用户名：管理员密码：管理员 Wi-Fi 2.4 GHz 网络名称（SSID）：华硕 Wi-Fi 5 GHz 网络名称（SSID）：默认情况下未设置华硕 5G Wi-Fi 密码。

请注意：如果您没有看到 “华硕 5G” 网络，则表示您的华硕路由器不支持 5.8 GHz 频段（例如，华硕 RT-N10P 不支持该频段），或者接收设备（计算机、电话、平板电脑等）没有 5.8 GHz 无线支持。在这种情况下，请使用 “华硕” 网络。

步骤 1“KDSP” 通过以太网电缆或无线方式连接到 ASUS 路由器。计算机应连接到 VPN 路由器的 LAN 端口，或连接到 ASUS 或 ASUS_5G 网络。

打开 web 浏览器，键入 192.168。在地址栏中输入 1.1，然后按 enter 键。它将提示您输入用户名 / 密码。默认值为：admin（用户名）和 admin（密码）。

登录后，转到左侧菜单中的 “VPN” 选项。选择 “OpenVPN 客户端” 选项卡。在 “选择客户端实例” 字段中选择“客户端 1”。

“KDSP”单击 “导入. ovpn 文件” 字段中的 “选择文件” 按钮。此配置文件是您在本教程开始时从客户区域获得的。如果您不确定从哪里获取，请向上滚动页面，您将在那里找到说明。

![](https://strongtech.org/assets/img/tutorials/setup_asuswrt_merlin_openvpn_step_01-266b54ef3e.png)

步骤 2

现在您需要选择。将 ovpn 配置文件上载到路由器上。此窗口将根据您的系统而有所不同。选择文件后，单击 “确定” 或“打开”按钮