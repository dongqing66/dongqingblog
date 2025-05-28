+++
date = '2025-05-20T11:37:18+08:00'
draft = false
title = 'My First Post'
+++

1.修改`frpc.toml`，新增多个`[[proxies]]`映射
2.放行端口  云服务器安全组和1panel或者宝塔面板 放行`7000`和所有`remotePort`端口（包含服务的映射端口）
3.进入/Users/zl/frp_0.62.0_darwin_arm64终端入口，然后运行./frpc -c ./frpc.toml
如果是运行中，按ctrl+C停止运行，然后再运行 ./frpc -c ./frpc.toml
4.测试访问用公网IP和映射端口访问对应服务

# FRP客户端新增端口映射标准流程（SOP）

## 1. 修改`frpc.toml`配置文件

- 在`[[proxies]]`里新增你要映射的端口配置，示例：
    

toml

```
[[proxies]]
name = "open-webui"
type = "tcp"
localIP = "127.0.0.1"
localPort = 3030
remotePort = 3030

[[proxies]]
name = "dockge"
type = "tcp"
localIP = "127.0.0.1"
localPort = 6030
remotePort = 6030

[[proxies]]
name = "woaini"
type = "tcp"
localIP = "127.0.0.1"
localPort = 6040
remotePort = 6040
```

- 注意：`remotePort`不能和已有端口冲突，若冲突，换成+1或其他未占用端口。
    

## 2. 放行端口

- 在云服务器安全组、1Panel或宝塔面板放行`7000`（服务端连接端口）和所有新增的`remotePort`端口。
    
- 确保防火墙规则生效。
    

## 3. 让配置生效

## 方法A：重启客户端（传统方式）

1. 进入`frpc`目录：
    

bash

```
cd /Users/zl/frp_0.62.0_darwin_arm64
```

2. 停止当前运行的`frpc`进程：
    

bash

```
pkill frpc
```

3. 重新启动`frpc`（后台运行）：
    

bash

```
nohup ./frpc -c ./frpc.toml > frpc.log 2>&1 &
```

4. 查看日志确认代理启动成功：
    

bash

```
tail -f frpc.log
```



## 4. 测试访问

- 用公网IP和新增的`remotePort`端口测试访问。
    

例如：

bash

```
ssh -p 6030 user@113.45.19.182
```

或浏览器访问：

text

```
http://113.45.19.182:3030
```

## 5. 额外建议

- **维护端口分配表**，避免端口冲突。
    
- **定期备份配置文件**，方便回滚。
    
- **开启日志监控**，及时发现异常。
    
- **安全组规则尽量精细**，只放行必要端口。
    

# 总结

|步骤|说明|
|---|---|
|修改配置|新增`[[proxies]]`，避免端口冲突|
|放行端口|云服务器安全组和防火墙放行端口|
|生效配置|重启frpc或使用`frpc reload`动态加载|
|测试访问|用公网IP+端口测试服务|
|维护管理|端口分配表、日志监控、配置备份|

这样你以后新增端口，只需修改配置+放行端口+热加载即可，流程简单高效。
