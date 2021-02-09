# 测试连接出现“不允许使用内网IP实例”怎么办？

本文介绍目标库为AnalyticDB数据库时，测试连接出现“不允许使用内网IP实例“的提示时的处理方法。

## 问题描述

当目标库为AnalyticDB数据库时，配置迁移或同步任务的过程中，出现连接测试失败，提示“不允许使用内网IP实例“。

![不允许使用内网IP](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3162582161/p240901.jpg)

## 可能原因

目标库AnalyticDB数据库暂不支持连接测试。

## 解决方案

当目标库为AnalyticDB数据库时，在确保用户名和密码正确的情况下，可不进行测试连接，直接进行后续的配置操作。

