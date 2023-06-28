---
title: Sealos
date: 12-12 112.35 
categories: 云原生（哔哩哔哩）
comments: "true"
tag: 云原生
---
# Sealos

是部署工具 ， 用go语言开发的干净且清凉的k8s的部署工具

# Sealos的优势

他的证书有效时间是100年

不依赖ansible haproxy keepalived 是零依赖

离线安装

kubelet其默认通过ipvs实现localLB，占用资源少稳定可靠

更容易在集群节点上增加/删除管理

上千用户使用，在阿里云oss上有，不用担心网速的问题

dashboard ingress prometheus 等 APP 同样支持离线打包安装

当不超过三个独立集群，都是免费的，超过则要订阅服务了

# rancher是目前企业中认知度最好的 ： 周六周日补上

# Sealos常用的参数

--master master的节点服务器地址

--node node节点服务器地址列表

--user 服务器 SSH 用户名

--password 服务器 SSH 用户的密码

--pkg-url 离线包所在的位置，可以是本地，也可以是http

--version 指定要部署的k8s的版本

 --pk 指定 SSH 私钥所在的位置 默认是在/root/.ssh/id_rsa

--podcidr 自定义 pod网段

--svccidr 参数指定clusterip网段

## 注意事项

需要纯净的linux ： centos7 对于centos8 需要调试很多的

或者是ubantu

尽量用新版本的Sealos

注意 ： 服务器时间必须同步

主机名字不可以重复

master的cpu要2个以上

cni组件选择cilium 时的内核版本不低于5.4

k8s一般用回退两个版本

# 部署

47.25
