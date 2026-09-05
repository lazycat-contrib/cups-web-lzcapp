# cups-web-lzcapp

把家用 USB 打印机变成随时可访问的网络打印服务（[hanxi/cups-web](https://github.com/hanxi/cups-web) 的懒猫微服打包）。

- 浏览器上传文件即可打印，支持打印队列管理、打印记录与多用户
- 支持 IPP 网络打印，手机 / 电脑可直接添加网络打印机
- 支持在 Web 界面在线安装厂商驱动，驱动持久化保存
- 内置 Debian 通用驱动包（printer-driver-all、hplip、brlaser 等），主流打印机开箱即用

## 打包说明

- 镜像：`hanxi/cups-web:v0.2.11`（通过 `lzc-cli appstore copy-image` 复制到懒猫仓库）
- `usb_accel: true` + `compose_override: privileged`：容器以 root 运行 cupsd / lpadmin，
  并可访问 USB 打印机设备节点（`usb_accel` 挂载 `/dev/bus/usb`）
- `application.ingress` 暴露 631 端口供 IPP 协议使用
- 数据持久化在 `/lzcapp/var`（CUPS 配置、数据库、上传文件、驱动）

## 构建

```bash
lzc-cli project release -o cups-web.lpk
```

## 自动更新

仓库配置了 [ca-x/lazycat-github-action](https://github.com/ca-x/lazycat-github-action)：

- 定时检查上游 `hanxi/cups-web` 的新版本 tag，自动发 PR 更新
- push tag 时自动构建 LPK 并发布 GitHub Release
- 需要配置 Secret：`LZC_API_TOKEN`（懒猫开放平台 PAT）

## 使用提示

1. 默认 Web 登录账号 `admin / admin`，登录后请尽快修改密码
2. CUPS 管理员账号密码在安装时的设置向导中配置（默认 `print / print`）
3. 添加打印机后需在 CUPS 中勾选 **Share（共享）**，Web 端才能发现打印机
4. mDNS/AirPrint 自动发现依赖宿主机 avahi 服务；若无法自动发现，
   可手动通过 631 端口的 IPP 地址添加打印机
