---
layout:     post
title:      "CentOS7防火墙放行/限制指定端口"
subtitle:   
date:       2023-06-26 13:00
author:     "Chenghao"
header-img: "img/post-bg-2015.jpg"
tags:
    - linux
    - CentOS 7
    - firewall
---

> 本文参考：https://www.cnblogs.com/zmqcoding/p/14700374.html

1. 查看 `firewalld.service` 服务状态
```
systemctl status firewalld
```

2. 查看 firewall 运行状态
```
firewall-cmd --state
```

3. 手动启动/停止/重启 `firewalld.service` 服务
```
# 启动 firewalld
service firewalld start
# 停止 firewalld
service firewalld stop
# 重启 firewalld
service firewalld restart
```

4. 展示当前配置的 firewall 规则
```
firewall-cmd --list-all
```

5. 端口的查询/开放
```
# 查询端口是否开放
firewall-cmd --query-port=8080/tcp
# 新建永久规则，开放8080端口（TCP协议）
firewall-cmd --permanent --add-port=8080/tcp
# 移除上述规则
firewall-cmd --permanent --remove-port=8080/tcp
# 新建永久规则，批量开放一段端口（TCP协议）
firewall-cmd --permanent --add-port=9001-9100/tcp
```

6. IP 的开放
```
# 新建永久规则，开放 192.168.1.1 单个源IP的访问
firewall-cmd --permanent --add-source=192.168.1.1
# 新建永久规则，开放 192.168.1.0/24 整个源IP段的访问
firewall-cmd --permanent --add-source=192.168.1.0/24
# 移除上述规则
firewall-cmd --permanent --remove-source=192.168.1.1
```

7. 系统服务的开放
```
# 开放http服务
firewall-cmd --permanent --add-service=http
# 移除上述规则
firewall-cmd --permanent --remove-service=http
```

8. 自定义复杂规则（注意是否与已有规则冲突）
```
# 允许指定IP访问本机8080端口
firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="192.168.1.1" port protocol="tcp" port="8080" accept'
# 允许指定IP段访问本机8080-8090端口
firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="192.168.1.0/24" port protocol="tcp" port="8080-8090" accept'
# 禁止指定IP访问本机8080端口
firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="192.168.1.1" port protocol="tcp" port="8080" reject'
```

9.  任何修改操作，配置完成后，需要重新装在 firewall。可重新启动 firewalld 服务
```
firewall-cmd --reload
service firewalld restart
```






