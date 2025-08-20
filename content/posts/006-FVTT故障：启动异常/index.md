+++
title = 'FVTT 报错：启动异常'
date = 2025-08-20T15:47:54+08:00
draft = false
tags = ['FVTT']
categories = ['应用']
+++

## 部署环境
- 腾讯云服务器 
- 源码部署

## 问题详述
理论上 FVTT 应该自启动，但某一次使用 SSH 连接服务器并关闭连接终端后，FVTT 再无法通过域名和 IP 访问。

- 使用`pm2 list`检查任务状态，发现`status:errored`，尝试`pm2 restart`，仍然报错。

- 使用`node /root/Projects/FVTT/foundryvtt/resources/app/main.js --dataPath=/root/Projects/FVTT/foundrydata`则可以成功启动。

## 解决方案

### 1. 创建守护进程

`sudo nvim /etc/systemd/system/fvtt.service` 创建守护进程文件，写入：

```bash
Description=foundryvtt
Documentation=https://foundryvtt.com/kb/
After=network.target

[Service]
Type=simple
User=Fvtt 
ExecStart=/usr/bin/node node /root/Projects/FVTT/foundryvtt/resources/app/main.js --dataPath=/root/Projects/FVTT/foundrydata
WorkingDirectory=/root/Projects/FVTT/foundryvtt
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

`sudo systemctl daemon-reload` 更新

`sudo systemctl start fvtt.service` 启动

`sudo systemctl enable fvtt.service` 激活

### 2. 重设 `pm2` 任务

`pm2 list` 检查任务列表

`pm2 delete FVTT` 删除 FVTT 任务

`cd /root/Projects/FVTT/foundryvtt/resources/app` 进入部署目录

`pm2 start --name FVTT /root/Projects/FVTT/foundryvtt/resources/app/main.js -- --dataPath=/root/Projects/FVTT/foundrydata` 重设 FVTT 任务

`pm2 list` 检查任务列表

`pm2 save` 保存任务列表

`pm2 kill` 清理任务列表，重置序号

`pm2 resurrect` 启动保存的列表任务
