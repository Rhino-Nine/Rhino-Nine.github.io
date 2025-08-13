+++
title = '无人深空基地复制FQA'
date = 2024-10-29T15:33:36+08:00
draft = false
tags = ['nms','blender']
categories = ['应用']
+++

# 步骤

## 一、复制存档建筑信息

- 启动存档修改器，备份存档，读取存档
- 复制 json 源文件内容

## 二、写入 blender

- 安装 blender 3.6 版本，安装并启用 NMS builder 插件和 bpy_internall 插件
- import 源文件内容，按需编辑，export
- 以新模型代码内容替换原 json 文件内容
- save changes
- 重启 NMS
