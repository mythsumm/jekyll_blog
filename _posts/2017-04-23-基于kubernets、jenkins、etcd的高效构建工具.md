---
title: '基于kubernets、jenkins、etcd的高效构建工具'
layout: post
tags:
    - kubernets
    - k8s
    - 持续集成
    - etcd
    - jenkins
    - docker
    - registry
    - minikube
    - kubectl
---

> Kubernetes是Google开源的容器集群管理系统，其提供应用部署、维护、 扩展机制等功能，利用Kubernetes能方便地管理跨机器运行容器化的应用。下面我将在macos上搭建minikube与kubectl的k8s环境。
>
> Jenkins是一个开源项目，提供了一种易于使用的持续集成系统，使开发者从繁杂的集成中解脱出来，专注于更为重要的业务逻辑实现上。同时Jenkins能实施监控集成中存在的错误，提供详细的日志文件和提醒功能，还能用图表的形式形象地展示项目构建的趋势和稳定性。
>
> Etcd是一个高可用的键值存储系统，主要用于共享配置和服务发现。下面利用etcd作为旁路系统，主要用来实现状态变更的定制化操作与配置同步。
>
>
> ## 环境搭建
>
> * mac os 10.12.3
> * docker [^docker]
> * jenkins [^jenkins]
> * registry [^registry]
> * minikube && kubectl [^minikube]
> * etcd [^etcd]

## 在k8s中创建应用
![deploy app in k8s](http://o94lfo79s.bkt.clouddn.com/67DCAA87-6BBD-4131-8C5C-4CFCEB5FFD44.jpg?e=1492962315&token=A_CmML41AGuqYDDfvh1Am3ztaJH9peHW9a8ZML88:30FNP1KCn6IOP3VB7PVDsMjkTPM)
此时应用所使用的镜像为*app-k8stest:2*

稍等片刻直到应用启动完毕！
![app runing](http://o94lfo79s.bkt.clouddn.com/2B6C0F33-6FDF-4EF5-B0A8-CD9276F437F0.jpg?e=1492962711&token=A_CmML41AGuqYDDfvh1Am3ztaJH9peHW9a8ZML88:xTTByp_bx3abBchr2FZAs289Uro)

![minikube service k8stest --url](http://o94lfo79s.bkt.clouddn.com/6054F559-28AD-4CA6-A07E-085384105B2D.jpg?e=1492962557&token=A_CmML41AGuqYDDfvh1Am3ztaJH9peHW9a8ZML88:xTfDniLvYesmEH5CvbMwcW8jBGI)

输入上述host可以打开应用。
![k8s app runing](http://o94lfo79s.bkt.clouddn.com/534EAA1B-0C67-4FEB-99DE-6CA9BDA3CD6F.jpg?e=1492962973&token=A_CmML41AGuqYDDfvh1Am3ztaJH9peHW9a8ZML88:r0APPuGRymFRk5eEnmO15UbX05g)


## 新建并配置jinkins工作空间
![配置jinkins工作空间](http://o94lfo79s.bkt.clouddn.com/E053F21A-E2AE-4BA2-82CB-8AD5D09F28EB.jpg?e=1492961022&token=A_CmML41AGuqYDDfvh1Am3ztaJH9peHW9a8ZML88:G7SzKRpJs7yZLGyzLpdpTyMTZPk)
构建的代码仓库为*git@github.com:mythsumm/k8s-test.git/test*
构建时需执行*build.sh*这个shell脚本，主要有三个步骤。
1. 用新提交的代码构建新镜像。
2. 将镜像打上标签并推到registry服务中。
3. 将容器中的镜像动态更新为刚build好的registry服务中的镜像。

```
#!/usr/bin/env sh

JOB_NAME=${JOB_NAME:-"k8stest"}
BUILD_NUMBER=${BUILD_NUMBER:-1}

REGISTRY_URL=${REGISTRY_URL:-"127.0.0.1:5000/liaotao/"}

#docker build
echo execute : docker build -t app-${JOB_NAME}:${BUILD_NUMBER} .
docker build -t app-${JOB_NAME}:${BUILD_NUMBER} .

#docker tag
echo execute : docker tag app-${JOB_NAME}:${BUILD_NUMBER} ${REGISTRY_URL}app-${JOB_NAME}:${BUILD_NUMBER}
docker tag app-${JOB_NAME}:${BUILD_NUMBER} ${REGISTRY_URL}app-${JOB_NAME}:${BUILD_NUMBER}

#docker push
echo execute : docker push ${REGISTRY_URL}app-${JOB_NAME}:${BUILD_NUMBER}
docker push ${REGISTRY_URL}app-${JOB_NAME}:${BUILD_NUMBER}

#Update existing container image(s) of resources
/usr/local/bin/kubectl set image deployment/k8stest k8stest=${REGISTRY_URL}app-${JOB_NAME}:${BUILD_NUMBER} --namespace=default --kubeconfig=/Users/ethanliao/.kube/config

```
构建过程中生成的镜像
![registry](http://o94lfo79s.bkt.clouddn.com/23807593-01B5-4B7D-BE07-957C9B822CB5.jpg?e=1492961022&token=A_CmML41AGuqYDDfvh1Am3ztaJH9peHW9a8ZML88:2Zk6RI3JwgLciA82lvGFArmpqz4)

如若此时修改*server.php*中的代码提交且在jenkins中再构建完毕，应用将使用最近一次构建的镜像。

```
-<?php
-var_dump($_SERVER);

+<?php
+phpinfo();
```
#### 构建：
![jenkins](http://o94lfo79s.bkt.clouddn.com/830129D2-8EC0-42BE-B7FD-9DB35B75025C.png?e=1492963706&token=A_CmML41AGuqYDDfvh1Am3ztaJH9peHW9a8ZML88:mQmxm6_OgVTBe3Vd6zeqlp7zsLI)

#### k8s中的镜像已经改变：
![images](http://o94lfo79s.bkt.clouddn.com/4A7CA8F5-E7C6-45CF-BF11-894742F48AAD.jpg?e=1492963706&token=A_CmML41AGuqYDDfvh1Am3ztaJH9peHW9a8ZML88:gQ98T56mWnv76X6eNUCUYLNbTZI)

#### 此时，应用也改变了。
![kubernetes app runing](http://o94lfo79s.bkt.clouddn.com/D59F45EF-E11A-4060-95E4-FD3D2A6EE079.png?e=1492963706&token=A_CmML41AGuqYDDfvh1Am3ztaJH9peHW9a8ZML88:J2jScy_tHiTxAtih9vNeBTpVieU)

### 若应用使用的变量经常发生改变，如数据库地址等，则完全没有必要再重复一遍上面的流程。可以搭建一套变量配置系统，当相关应用所使用的变量发生改变时则改变其在etcd中的值，如若此时该应用一直侦听etcd中的值，就可以同步至最新的变量。
1. 搭建etcd服务器
2. 变量配置系统中用etcd设置值 *composer update "linkorb/etcd-php": "1.2.1" && $client->set('***', '***');*
3. 容器在启动后运行*bypass.sh*循环监听值的变化

##  结束语
如上所述，则可以搭建一套高效的构建工具。既可以使用k8s的灵活伸缩性，也能方便的配置变量等...。


## refs:

[^docker]: [Mac OS 安装 Docker](https://github.com/widuu/chinese_docker/blob/master/installation/mac.md)

[^jenkins]: [Mac OS 安装 Jenkins](http://www.jianshu.com/p/ab3302cd68eb)

[^registry]: [run registry in docker](https://hub.docker.com/_/registry/)

[^minikube]: [Mac OS 安装 kubernetes](http://www.jianshu.com/p/a42eeb66a19c)

[^etcd]: [run etcd in docker](https://hub.docker.com/r/microbox/etcd/)





