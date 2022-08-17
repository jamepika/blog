---
title: 技术分享-setcap命令
description: setcap运用案例
categories:
- linux
---

### setcap运用案例

**问题背景**

​	开发人员在测试环境搭建一套nginx系统自行维护，hvv行动之后根据相关要求进行环境整改，安装中间件标准nginx环境。

- nginx版本:1.18--->1.22
- 启动用户：isearch--->wlsoper
- 模块：自添加nginx-upload-module模块(待研究)---->新版本需要重新编译手动添加项目组需要的模块。

**环境场景**

​	通过自动化运维平台推送nginx安装脚本然后再手动编译(过程略)所需模块修改nginx端口为443(配置文件中配置了ssl)，启动nginx报错如下

![](https://raw.githubusercontent.com/jamepika/picgo/main/work/202208151716654_nginx_error.jpeg)

**问题解决**

​	网上一顿搜索发现linux中普通用户是无法使用1024以下端口的权限的，如果nginx使用root用户启动则不存在此问题，但我们的标准环境必须使用普通用户进行启动，且443端口提供的nginx服务还需要其他服务对它进行调用，如果修改为大于1024端口的话，其他服务也需要修改相关配置，影响比较大。

​	因此，最终使用如下命令给普通用户添加权限使其能够使用小于1024以下的端口

```shell
setcap cap_net_bind_service=+ep  （nginx命令路径）如/opt/nginx-1.22/sbin/nginx
```

![](https://raw.githubusercontent.com/jamepika/picgo/main/work/202208151717286_setcap%E5%8F%82%E6%95%B0%E8%A7%A3%E9%87%8A.png)

参考链接：<https://man7.org/linux/man-pages/man7/capabilities.7.html>

