---
layout: post
title: 'WSL2通过proxychains4访问主机代理'
date: '2021-01-31'
categories: [WSL]
---

## WSL2连接到主机代理

- Windows 和 WSL2 算是在同一个局域网内，这个局域网是由 Hyper-V 创建的。

- WSL2 使用的网络适配器是 ‘Default Hyper-V Switch’，这个适配器每次重启都会被删除重建，这就是 WSL2 为什么 IP 不固定的原因。

- 安装proxychains4
```bash
sudo apt-get install proxychains4
```

- 将如下配置写入shell配置文件，即`.zshrc`或者`.bashrc`
```bash
# 从resolv.conf获取当前IP
export hostIP=`grep -oP  "(\d+\.)+(\d+)" /etc/resolv.conf`
# 将port设置为固定值1080
export hostPort=1080
# 将其写入到proxychains4配置文件中
sudo sed -i "115c socks5 $hostIP $hostPort" /etc/proxychains4.conf
# 为proxychains4设置别名
alias px="proxychains4"
```

- 设置完成后，执行`source .zshrc`更新配置

- 如果无法连接，可能是主机防火墙原因，在主机powershell使用管理员执行以下命令
```
New-NetFirewallRule -DisplayName "WSL" -Direction Inbound -InterfaceAlias "vEthernet (WSL)" -Action Allow
```